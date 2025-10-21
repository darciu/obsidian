W folderze pkg/data_products/datasets należy stworzyć plik .go z nazwą datasetu.

Templatkę datasetu można znaleźć w repo high-cpm-fs-client (pkg/data_products/datasets/test_dataset.go), którą można sobie przekleić i zamienić odpowiednie importy i inne nazwy z hig na aip-fs.

Należy wybrać datę przeliczania datasetu

Wszędzie gdzie w pliku jest TestDataset, należy zmienić nazwę na własną.

testDsFeatures - ustawienia Datasetu

Jeśli nie ma hoppingów, slidingów, composite features, to kasuję wnętrze, np. testDsSlidingFeatures, ale zostawiając tą zmienną