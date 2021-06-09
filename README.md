# Проект по биоинформатике: эпигенетика и вторичные стукруты ДНК

* Организм: **Human**
* Сборка генома: **Hg19**
* Тип исследуемых клеток: **H9**
* Гистоновая метка: **H3K9me3**
* Структура ДНК: **G4**

UCSC GenomeBrowser session: https://genome-euro.ucsc.edu/s/Oureal/H3K9me3_H9

## Анализ пиков гистоновой метки
Скачиваем необходимые файлы

`wget https://www.encodeproject.org/files/ENCFF073SPO/@@download/ENCFF073SPO.bed.gz`

`wget https://www.encodeproject.org/files/ENCFF305RWK/@@download/ENCFF305RWK.bed.gz`

Оставляем первые пять столбцов

`zcat ENCFF073SPO.bed.gz | cut -f1-5 > H3K9me3_H9.ENCFF073SPO.hg38.bed`

`zcat ENCFF305RWK.bed.gz | cut -f1-5 > H3K9me3_H9.ENCFF305RWK.hg38.bed`

Приводим координаты ChIP-seq пиков к нужной версии генома (Hg38 -> Hg19)  
Для этого скачаем файл, необходимый для `liftOver`

`wget https://hgdownload.cse.ucsc.edu/goldenpath/hg38/liftOver/hg38ToHg19.over.chain.gz`

`liftOver H3K9me3_H9.ENCFF073SPO.hg38.bed hg38ToHg19.over.chain.gz H3K9me3_H9.ENCFF073SPO.hg19.bed H3K9me3_H9.ENCFF073SPO.unmapped.bed`

`liftOver H3K9me3_H9.ENCFF305RWK.hg38.bed hg38ToHg19.over.chain.gz H3K9me3_H9.ENCFF305RWK.hg19.bed H3K9me3_H9.ENCFF305RWK.unmapped.bed`

### Гистограмма длин участков для каждого эксперимента до переноса на Hg19

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/len_hist.H3K9me3_H9.ENCFF073SPO.hg38.png)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/len_hist.H3K9me3_H9.ENCFF305RWK.hg38.png)

### Гистограмма длин участков для каждого эксперимента после переноса на Hg19 

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/len_hist.H3K9me3_H9.ENCFF073SPO.hg19.png)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/len_hist.H3K9me3_H9.ENCFF305RWK.hg19.png)

### Отфильтруем пики длиной больше 5000 с помощью скрипта [filter_peaks.R ](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/src/filter_peaks.R)

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/filter_peaks.H3K9me3_H9.ENCFF073SPO.hg19.filtered.hist.png)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/filter_peaks.H3K9me3_H9.ENCFF305RWK.hg19.filtered.hist.png)

### Расположение пик гистоновой метки относительно аннотированных генов  
Построено с помощью [chip_seeker.R](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/src/chip_seeker.R)

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.H3K9me3_H9.ENCFF073SPO.hg19.filtered.plotAnnoPie.png)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.H3K9me3_H9.ENCFF305RWK.hg19.filtered.plotAnnoPie.png)

Объединяем два набора отфильтрованных ChIP-seq пиков с помощью утилиты `bedtools merge`  
Для запуска команды необходимо отсортировать единый .bed файл

`cat *.filtered.bed | sort -k1,1 -k2,2n | bedtools merge > H3K9me3_H9.merge.hg19.bed`

Визуализируем исходные два набора ChIP-seq пиков, а также их объединение в геномном браузере, и проверим корректность работы bedtools merge

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/genome_browser_merge.png)

## Анализ участков вторичной структуры ДНК

Скачиваем файл со вторичной структурой ДНК (квадруплексы)

`wget https://ftp.ncbi.nlm.nih.gov/geo/series/GSE99nnn/GSE99205/suppl/GSE99205_common_HaCaT_G4_ChIP_peaks_RNase_treated.bed.gz`

Удаляем символ переноса каретки в конце каждой строки, чтобы `bedtools intersect` отработал корректно

`cat GSE99205_common_HaCaT_G4_ChIP_peaks_RNase_treated.bed | tr -d '\r' > G4_ChIP_peaks.clean.bed`

### Распределение длин участков квадруплексов

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.G4_ChIP_peaks.clean.plotAnnoPie.png)

###  Расположение квадруплексов относительно аннотированных генов

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.G4ChipIntersect.plotAnnoPie.png)

## Анализ пересечений гистоновой метки и квадруплексов

Находим пересечения между гистоновой меткой и структурами ДНК

`bedtools intersect -a G4_ChIP_peaks.clean -b H3K9me3_H9.merge.hg19 > G4ChipIntersect.bed`

### Распределения длин пересечений гистоновой метки и структур ДНК

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/len_hist.G4ChipIntersect.png)

Визуализируем в геномном браузере исходные участки структуры ДНК, а также их пересечения с гистоновой меткой

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/genome_browser_intersect.png)

Ассоциируем полученные пересечения с ближайшими генами с помощью R-библиотеки ChIPpeakAnno  
Скрипт [ChIPpeakAnno.R](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/src/ChIPpeakAnno.R)

Всего удалось ассоциировать 499 пиков. [Ассоциации пиков](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/data/G4ChipIntersect.genes.txt)  
Общее число уникальных генов - 463. [Список генов](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/data/G4ChipIntersect.genes_uniq.txt)

## GO-анализ

С помощью [Panther](http://pantherdb.org/) проведем GO анализ
 
### Наиболее значимые биологические процессы
 
![alt text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/GO_analisys.png)

[Полные результаты анализа](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/data/pantherdb_GO_analysis.txt)
