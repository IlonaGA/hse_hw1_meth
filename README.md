# hse_hw1_meth
[Ссылка на Google Colab](https://colab.research.google.com/drive/1-dXmptBlP0_1-4jG2QZhg2dha5yFg3Vt#scrollTo=f-eP1RzVux4x)  

### FastQC report
Отчет html находится в директории reports. Для получения отчета был скачен запуск с ресурса European Nucleotide Archive, а затем запущен FastQC. 
Особенности:   
В пункте Per base sequence content наблюдаются различия между процентным содержанием A и T, а также G и C (в "норме" их количество должно примерно совпадать в рамках 1 пары A/T и G/C).  
В разделе Per sequence GC content наблюдается 2 пика, вместо привычного одного, что вызывает сильное различие с Theoretical distribution.  
Вид графика Sequence Length Distribution в норме напоминает пирамиду или треугольник, здесь же наблюдается другой график.




### Работа с bam-files, выравниваниями BS-seq ридов на 11-ю хромосому мыши (samtools view):
Cводная таблица ридов, закартированных на участки 11347700-11367700 и 40185800-40195800 + дедупликация:
| Название образца  | 11347700-11367700 | 40185800-40195800 | Дедупликация |
| ------------- | ------------- | ------------- | ------------- |
| cell8  | 1090  | 464 | 81.69% |
| epiblast  | 2328 | 1062 | 97.08$ |
| ICM  | 1456 | 630 | 90.92% |

Команда bash позволяющая выполнить дедупликацию всех образцов одновременно:
```
! ls *pe.bam | xargs -P 4 -l -I{} deduplicate_bismark --bam --paired -o outputname_{} {}
```
Далее был проведен коллинг метилирования цитозинов. Отчет в формате html находится в директории reports.   
#### M-bias plot:

**8cell:**   
| 1 | 2 |
| ------------- | ------------- |  
| ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/8cell1.png)  |  ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/8cell2.png) |

**epiblast:**  
| 1 | 2 |
| ------------- | ------------- | 
| ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/epiblast1.png) | ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/epiblast2.png) |

**ICM:**   
| 1 | 2 |
| ------------- | ------------- | 
| ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/ICM1.png) |  ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/ICM2.png) |

На графиках показана доля метилирования на разных позициях хромосомы. Легко видеть, что наиболее метилированным оказался образец Epiblast, затем идет 8cell, затем ICM (очень слабо метилированный образец).  

### Гистограммы распределения метилирования цитозинов по хромосоме (по X процент метилированных цитозинов, по Y - частота):  
| 1 | 2 | 3 |
| ------------- | ------------- | ------------- | 
| ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/hist_8cell.png) | ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/hist_epiblast.png) | ![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/hist_ICM.png) |   

Для образца Epiblast наблюдается наибольший уровень метилирования ДНК (много 100 и около, относительно мало 0). Обратная ситуация для образца ICM (много 0). 8cell находится также метилирован, но заметно меньше чем Epiblast.   

Для построения был использован python:
```
df = pd.read_csv('s_TYPE.deduplicated.bedGraph', sep='\t', skiprows=1, header=None)
fig = plt.figure()
plt.title('TYPE')
plt.hist(df[3], bins=50)
plt.xlabel('Процент метилированных цитозинов')
plt.ylabel('Частота')
plt.show()
fig.savefig('TYPE.png')
```

### Уровень метилирования и покрытия для каждого образца: 
**Метилирование:**  
![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/image_methylation.png)
**Покрытие:**  
![ ](https://github.com/IlonaGA/hse_hw1_meth/blob/main/images/image_coverage.png)

**Вывод**: можем наблюдать различия в доле метилирования (волны деметилирования-метилирования) на разных стадиях развития эмбриона, что согласуется с картинкой и источником ниже.
![ ](https://en.wikipedia.org/wiki/File:DNA_methylation_reprogramming.png)
[Ссылка](https://en.wikipedia.org/wiki/DNA_methylation#During_embryonic_development)
