AB-tests-service
https://ab-tests-service-abtests-http.nginx.services.dc-2.lb.dcwp.pl/ui/

Mała ćma (Growthbook)
http://high-cpm-growthbook.ma.dc-2.lb.dcwp.pl/features

interesujące namespace: wppl_desktop, wppl_mobile_web

Na wppl_desktop działają jednocześnie 3 testy: 
random
pinned - domyślne tesy, które są porównywalne z randomem; dwa testy odniesienia; robimy zazwyczaj dwie grupy testowe dla każdego testu, gdyż testy AA też mogą czasami się różnić
statid parent
procenty w podtestach (w galęziach) mogą sumować się do 100%. Jesli podtesty nie zajmują całego ruchu, to ruch trafia do parent testu.


Wykilkać sprawdzanie testów w metabase

```
with
     'pinned_one|lints|linucb_ucb|lin_ucb_geo_h3|old_ucb' as pattern,
     ('pinned_one_a', 'pinned_one_m_a') as reference_groups,
     '0' as short_variants, -- czy skracać nazwy wariantów o _a/_b?
     2 as precision,
     raw_data as (
        select
        mobileView,
        if(short_variants = '1', replaceRegexpAll(abTestAnalytics, '(_a|_b)$', ''), abTestAnalytics) AS variant_group,
        --uniq(statid) as uu,
        countIf(action = 'click' and teaserType  in ('sgTeaser', 'sgTextlink')) as clicks, 
        countIf(action = 'view') as views, 
        countIf(action = 'display') as displays,
        100 * clicks / countIf(action = 'display')  as ctr
        --clicks / uu  as ctu,
        --countIf(action = 'click' and teaserType  in ('sgTeaser', 'sgTextlink') and hasUserSketch == 1) as clicks_w_sktchs,
    from moth.contentData_all
    prewhere 
        date between {{start_date}} and {{end_date}}
        and timestamp >= '2025-05-09 11:50:00'
        and mothType = 'bigMoth'
        and action in ('click','view','display', 'backendPV')
        and timestamp <= now() - interval 2 minute
        and match(abTestAnalytics,pattern)
        and not match(abTestAnalytics,'svId_')
        -- and mobileView = '1'
    group by
        mobileView,
        variant_group
    having length(variant_group) > 0 and views > 100000
    order by mobileView, clicks desc
    ),
    ref as (
        select *
        from raw_data
        where variant_group in reference_groups
    )
select
    ref.variant_group as reference_group,
    rd.*, 
    --toString(round(100*(rd.uu/ref.uu-1), precision)) || '%' as REL_uu,
    toString(round(100*(rd.clicks/ref.clicks-1), precision)) || '%' as REL_clicks,
    toString(round(100*(rd.views/ref.views-1), precision)) || '%' as REL_views,
    toString(round(100*(rd.displays/ref.displays-1), precision)) || '%' as REL_displays,
    toString(round(100*(rd.ctr/ref.ctr-1), precision)) || '%' as REL_ctr--,
    --round(100*(rd.ctu/ref.ctu-1), 4) as ref_ctu
    --toString(round(100*(rd.clicks_w_sktchs/ref.clicks_w_sktchs-1), precision)) || '%' as REL_clicks_w_sktchs
from
    raw_data as rd
left join
    ref
on 
    (rd.mobileView = ref.mobileView)
```


Dać znać wszystkim, że zostało wdrożone i o której godzinie zaczną się testy


pod testem pinned_one jest bandit serving
