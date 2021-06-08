# Проект по биоинформатике: эпигенетика и вторичные стукруты ДНК

* Организм: **Human**
* Сборка референсного генома: **Hg19**
* Тип исследуемых клеток: **H9**
* Гистоновая метка: **H3K9me3**

UCSC GenomeBrowser session: https://genome-euro.ucsc.edu/s/Oureal/H3K9me3_H9

## Выполнение проекта
С помощью команды `wget` были получены необходимые файлы

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

![alt-text]()
![alt-text]()

### Гистограмма длин участков для каждого эксперимента после переноса на Hg19 

![alt-text]()
![alt-text]()

### Отфильтруем пики длиной больше 5000 с помощью скрипта filter_peaks.R 

![alt-text]()
![alt-text]()

### Location of ChIP-seq peaks

![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.H3K9me3_H9.ENCFF073SPO.hg19.filtered.plotAnnoPie.png)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.H3K9me3_H9.ENCFF305RWK.hg19.filtered.plotAnnoPie.png)

### Location of DNA secondary structures(G4)
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.G4_ChIP_peaks.clean.plotAnnoPie.png)

### Location of intersection between ChIP-seq peaks and secondary DNA structures
![alt-text](https://raw.githubusercontent.com/YaroslavSolo/hse21_H3K9me3_G4_human/main/images/chip_seeker.G4ChipIntersect.plotAnnoPie.png)
