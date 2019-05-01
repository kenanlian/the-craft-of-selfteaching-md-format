
# 文件

我们需要处理的数据，一定是很多，所以才必须由计算机帮我们处理 —— 大量的数据保存、读取、写入，需要的就是文件（Files）。在这一章里，我们只介绍最简单的文本文件。

## 创建文件

创建一个文件，最简单的方式就是用 Python 的内建函数 `open()`。

`open()` 函数的[官方文档](https://docs.python.org/3/library/functions.html#open)很长，以下是个简化版：

> `open(file, mode='r')`

第二个参数，`mode`，默认值是 `'r'`，可用的 `mode` 有以下几种：

| 参数字符 | 意义                            |
| -------- | ------------------------------- |
| `'r'`    | 只读模式                        |
| `'w'`    | 写入模式（重建）|
| `'x'`    | 排他模式 —— 如果文件已存在则打开失败 |
| `'a'`    | 追加模式 —— 在已有文件末尾追加      |
| `'b'`    | 二进制文件模式                  |
| `'t'`    | 文本文件模式（默认）|
| `'+'`    | 读写模式（更新）|

创建一个新文件，用这样一个语句就可以：


```python
open('/tmp/test-file.txt', 'w')
```




    <_io.TextIOWrapper name='test-file.txt' mode='w' encoding='UTF-8'>



当然，更多的时候，我们会把这个函数的返回值，一个所谓的 [file object](https://docs.python.org/3/glossary.html#term-file-object)，保存到一个变量中，以便后面调用这个 file object 的各种 Methods，比如获取文件名 `file.name`，比如关闭文件 `file.close()`：


```python
f = open('/tmp/test-file.txt', 'w')
print(f.name)
f.close()
```

    test-file.txt
    

## 删除文件

删除文件，就得调用 `os` 模块了。删除文件之前，要先确认文件是否存在，否则删除命令会失败。


```python
import os

f = open('/tmp/test-file1.txt', 'w')
print(f.name)
f.close() #关闭文件，否则无法删除文件
if os.path.exists(f.name):
    os.remove(f.name)
    print(f'{f.name} deleted.')
else:
    print(f'{f.name} does not exist.')
```

    test-file1.txt
    test-file1.txt deleted.
    

## 读写文件

创建文件之后，我们可以用 `f.write()` 把数据写入文件，也可以用 `f.read()` 读取文件。


```python
f = open('/tmp/test-file.txt', 'w')
f.write('first line\nsecond line\nthird line\n')
f.close()

f = open('/tmp/test-file.txt', 'r')
s = f.read()
print(s)
f.close()
```

    first line
    second line
    third line
    
    

文件有很多行的时候，我们可以用 `file.readline()` 操作，这个 Method 每次调用，都会返回文件中的新一行。


```python
f = open('/tmp/test-file.txt', 'w')
f.write('first line\nsecond line\nthird line\n')
f.close()

f = open('/tmp/test-file.txt', 'r')
s = f.readline()    # 返回的是 'first line\n'
print(s)
s = f.readline()    # 返回的是 'second line\n'
print(s)
f.close()
```

    first line
    
    second line
    
    

**注意**，返回结果好像跟你想的不太一样。这时候，之前见过的 `str.strip()` 就派上用场了：


```python
f = open('/tmp/test-file.txt', 'w')
f.write('first line\nsecond line\nthird line\n')
f.close()

f = open('/tmp/test-file.txt', 'r')
s = f.readline().strip()    # 返回的是 'first line'，'\n' 被去掉了……
print(s)
s = f.readline().strip()    # 返回的是 'second line'，'\n' 被去掉了……
print(s)
f.close()
```

    first line
    second line
    

与之相对的，我们可以使用 `file.readlines()` 这个命令，将文件作为一个列表返回，列表中的每个元素对应着文件中的每一行：


```python
f = open('/tmp/test-file.txt', 'w')
f.write('first line\nsecond line\nthird line\n')
f.close()

f = open('/tmp/test-file.txt', 'r')
s = f.readlines()    # 返回的是一个列表，注意，readlines，最后的 's'
print(s)
f.close()
```

    ['first line\n', 'second line\n', 'third line\n']
    

既然返回的是列表，那么就可以被迭代，逐一访问每一行：


```python
f = open('/tmp/test-file.txt', 'w')
f.write('first line\nsecond line\nthird line\n')
f.close()

f = open('/tmp/test-file.txt', 'r')
for line in f.readlines():
    print(line)
f.close()
```

    first line
    
    second line
    
    third line
    
    

与之相对的，我们也可以用 `file.writelines()` 把一个列表写入到一个文件中，按索引顺序（从 0 开始）逐行写入列表的对应元素：


```python
a_list = ['first line\n', 'second line\n', 'third line\n']
f = open('/tmp/test-file.txt', 'w')
f.writelines(a_list)
f.close()

f = open('/tmp/test-file.txt', 'r')
for line in f.readlines():
    print(line)
f.close()
```

    first line
    
    second line
    
    third line
    
    

## with 语句块

针对文件操作，Python 有个另外的语句块写法，更便于阅读：

```python
with open(...) as f:
    f.write(...)
    ...
```

这样，就可以把针对当前以特定模式打开的某个文件的各种操作都写入同一个语句块了：


```python
import os

with open('/tmp/test-file.txt', 'w') as f:
    f.write('first line\nsecond line\nthird line\n')
    
with open('/tmp/test-file.txt', 'r') as f:
    for line in f.readlines():
        print(line)

if os.path.exists(f.name):
    os.remove(f.name)
    print(f'{f.name} deleted.')
else:
    print(f'{f.name} does not exist.')    
```

    first line
    
    second line
    
    third line
    
    test-file.txt deleted.
    

另外，用 `with` 语句块的另外一个附加好处就是不用写 `file.close()` 了……

## 另一个完整的程序

若干年前，我在写某本书的时候，需要一个例子 —— 用来说明 “**即便是结论正确，论证过程乱七八糟也不行！**”

作者就是这样，主要任务之一就是给论点找例子找论据。找得到不仅*恰当*且又*精彩*的例子和论据的，就是好作者。后面这个 “*精彩*” 二字要耗费很多时间精力，因为它意味着说 “要找到*很多*例子而后在里面选出*最精彩*的那个！” —— 根本不像很多人以为的那样，是所谓的 “信手拈来”。

找了很多例子都不满意…… 终于有一天，我看到这么个说法：

> 如果把字母 `a` 计为 `1`、`b` 计为 `2`、`c` 计为 `3` …… `z` 计为 `26`，那么：
>
> - knowledge = 96
> - hardwork = 98
> - attitude = 100
>
> 所以结论是：
>
> - 知识（*knowledge*）与勤奋（*hardwork*）固然都很重要；
> - 但是，决定成败的却是态度（**attitude**）！

结论虽然有道理 —— 可这论证过程实在是太过分了罢……

我很高兴，觉得这就是个*好例子*！并且，加工一下，会让读者觉得很精彩 —— 如果能找到一些按照同样的计算方式能得到 100 的单词，并且还是那种一看就是 “反例” 的单词……

凭直觉，英文单词几十万，如此这般等于 100 的单词岂不是数不胜数？并且，一定会有很多负面意义的单词如此计算也等于 100 罢？然而，这种事情凭直觉是不够的，手工计算又会被累死…… 于是，面对如此荒谬的论证过程，我们竟然 “无话可说”。

幸亏我是会写程序的人。所以，不会 “干着急没办法”，我有能力让计算机帮我把活干了。

很快就搞定了，找到很多很多个如此计算加起来等于 100 的英文单词，其中包括：

> - connivance（纵容）
> - coyness（羞怯）
> - flurry（慌张）
> - impotence（阳痿）
> - stress（压力）
> - tuppence（微不足道的东西）
> - ……

所以，决定成败的可以是 “慌张”（flurry），甚至是 “阳痿”（impotence）？这不明显是胡说八道嘛！

—— 精彩例子制作完毕，我把它放进了书里。

那，具体的过程是什么样的呢？

首先我得找到一个英文单词列表，很全的那种。这事用不着写程序，Google 一下就可以了。我搜索的关键字是 “[english word list](https://www.google.com/search?q=english+word+list)”，很直观吧？然后就找到一个：[https://github.com/dwyl/english-words](https://github.com/dwyl/english-words)；这个链接里有一个 [words-alpha.txt](https://raw.githubusercontent.com/dwyl/english-words/master/words_alpha.txt) 文件，其中包含接近 37,0101 个单词，应该够用了！下载下来用程序处理就可以了！

因为文件里每行一个单词，所以，就让程序打开文件，将文件读入一个列表，而后迭代这个列表，逐一计算那个单词每个字母所代表的数字，并加起来看看是否等于 100？如果是，就将它们输出到屏幕…… 好像不是很难。


```python
with open('words_alpha.txt', 'r') as file:
    for word in file.readlines():
        pass # 先用 pass 占个位，一会儿再写计算过程
```

按照上面那说法，把 `a` 记为 `1`，直至把 `z` 记为 `26`，这事并不难，因为有 `ord()` 函数啊 —— 这个函数返回字符的 Unicode 编码：`ord('a')` 的值是 `97`，那按上面的说法，用 `ord('a') - 96` 就相当于得到了 `1` 这个数值…… 而 `ord('z') - 96` 就会得到 `26` 这个数值。


```python
ord('a')
```




    97



那么，计算 `'knowledge'` 这个字符串的代码很简单：


```python
word = 'knowledge'
sum = 0
for char in word:
    sum += ord(char) - 96
print(sum)
```

    96
    

果然，得到的数值等于 `96` —— 不错。把它写成一个函数罢：`sum_of_word(word)`:


```python
def sum_of_word(word):
    sum = 0
    for char in word:
        sum += ord(char) - 96
    return sum

sum_of_word('attitude')
```




    100



那让程序就算把几十万行都算一遍也好像很简单了：


```python
def sum_of_word(word):
    sum = 0
    for char in word:
        sum += ord(char) - 96
    return sum

with open('words_alpha.txt', 'r') as file:
    for word in file.readlines():
        if sum_of_word(word) == 100:
            print(word)
```

    abstrusenesses
    
    acupuncturist
    
    adenochondrosarcoma
    
    adenomyxosarcoma
    
    adscititiously
    
    adsorptiveness
    
    anaglyptography
    
    anesthetization
    
    anisophyllous
    
    annihilationistic
    
    anomophyllous
    
    anthropopathite
    
    anthropophaginian
    
    antiagglutinating
    
    antianaphylactogen
    
    antibacteriolytic
    
    antichristianism
    
    anticyclolysis
    
    anticytolysin
    
    anticommutative
    
    anticonvulsive
    
    antieducationally
    
    antiexpressive
    
    antimilitaristic
    
    antimissionary
    
    antipestilential
    
    antiprofiteering
    
    antirepublicanism
    
    aponogetonaceous
    
    apostrophising
    
    apperceptionist
    
    aristocratically
    
    aristodemocratical
    
    arthrolithiasis
    
    associationalist
    
    assortatively
    
    astigmatometry
    
    atherosclerosis
    
    autoeciousness
    
    autointoxicant
    
    autoprothesis
    
    autosymbiontic
    
    benzoglyoxaline
    
    benzothiodiazole
    
    bioastronautics
    
    bisymmetrically
    
    blennocystitis
    
    carposporangium
    
    chondropterygii
    
    chromolithograph
    
    cytoplasmically
    
    cochromatography
    
    collenchymatous
    
    commercialization
    
    communalization
    
    competitorship
    
    complementalness
    
    compossibility
    
    congressionist
    
    conjecturableness
    
    consolitorily
    
    contemplatingly
    
    contradictorily
    
    contrapositive
    
    controllableness
    
    cooperativeness
    
    cosponsorship
    
    counsellorship
    
    counterpassant
    
    counterpotent
    
    counterspying
    
    countertendency
    
    counterworker
    
    countrywomen
    
    craspedodromous
    
    cryptanalytics
    
    cryptostomata
    
    cultivatability
    
    cultrirostral
    
    curvilinearity
    
    dacryostenosis
    
    dehydrosparteine
    
    demonstrability
    
    demonstrations
    
    denominationalize
    
    dephlogistication
    
    dermorhynchous
    
    desophistication
    
    desoxycinchonine
    
    desterilization
    
    diathermotherapy
    
    dyschromatopsia
    
    discriminatingly
    
    dyscrystalline
    
    discursiveness
    
    disenfranchisement
    
    disfranchisements
    
    disgustingness
    
    dispensatorily
    
    draftswomanship
    
    duplicitously
    
    electrobiologist
    
    electrocardiograms
    
    electrodepositable
    
    electromagnetist
    
    electromuscular
    
    electroresection
    
    electroviscous
    
    embourgeoisement
    
    endotheliolysin
    
    epistolography
    
    erythrolitmin
    
    erythropoietic
    
    eurytopicity
    
    eventognathous
    
    existentialist
    
    experientialist
    
    experimentative
    
    expostulator
    
    exquisiteness
    
    extemporalness
    
    exterminations
    
    externalisation
    
    extraconscious
    
    extradictionary
    
    ferromolybdenum
    
    fibroligamentous
    
    firmisternous
    
    flavorlessness
    
    flavorousness
    
    forerunnership
    
    forthrightness
    
    frictionlessly
    
    fruitlessness
    
    gastrolatrous
    
    geomorphogenist
    
    gymnospermism
    
    gyrophoraceous
    
    gyrostabilizer
    
    governmentalize
    
    gravitationally
    
    guanidopropionic
    
    hastatosagittate
    
    helminthologist
    
    hematocytometer
    
    hemotherapeutics
    
    hydrargyrosis
    
    hydroergotinine
    
    hydronitrous
    
    hydrophyllium
    
    hydroquinoline
    
    hydrotherapist
    
    hyperalkalinity
    
    hyperbrachycephalic
    
    hyperimmunized
    
    hyperthermally
    
    hyporrhythmic
    
    hyposensitive
    
    hypostatically
    
    hypostomous
    
    hippocastanaceous
    
    hipponosology
    
    hipponosological
    
    hysterocleisis
    
    historiographer
    
    homeomorphisms
    
    homotransplant
    
    iliohypogastric
    
    immunologists
    
    immunotherapies
    
    imposturous
    
    impoverishment
    
    impropriatrix
    
    incommutability
    
    inconstantness
    
    incorporations
    
    incuriousness
    
    indispensableness
    
    industrializes
    
    intensifications
    
    intensitometer
    
    intercursation
    
    interirrigation
    
    interjectionary
    
    interpretress
    
    intersituating
    
    interstimulate
    
    intraformational
    
    intraleukocytic
    
    intuitionless
    
    intuitiveness
    
    intussuscept
    
    irrespectively
    
    isopropylacetic
    
    yttrogummite
    
    lactiferousness
    
    laryngocentesis
    
    levorotatory
    
    limonitization
    
    liverwursts
    
    locomotiveness
    
    lotophagously
    
    lubriciousness
    
    luciferousness
    
    maldistribution
    
    mammatocumulus
    
    manuscriptural
    
    martyrolatry
    
    masculinization
    
    melanospermous
    
    mercuriammonium
    
    mesmerizability
    
    mesopterygium
    
    metempsychosical
    
    metropolitancy
    
    metroscirrhus
    
    mycosymbiosis
    
    microphyllous
    
    microprocessor
    
    ministerialness
    
    myodynamometer
    
    myringodectomy
    
    misinterpretable
    
    monoeciousness
    
    monopropellant
    
    monumentalising
    
    morphologists
    
    multicomputer
    
    multilinguist
    
    multimetallist
    
    multistratified
    
    necessitously
    
    nesslerization
    
    neurapophysis
    
    neurilemmatous
    
    neurosurgery
    
    neutroclusion
    
    nonagricultural
    
    nonappointment
    
    nonarticulation
    
    nonattainability
    
    nonbituminous
    
    nonblunderingly
    
    noncohesiveness
    
    noncollectively
    
    noncommittally
    
    nonconstraining
    
    noncontiguity
    
    noncruciformly
    
    noncuriously
    
    nondeductibility
    
    nonderogatively
    
    nondisruptive
    
    nondissipatedly
    
    nondistortion
    
    nonegregiously
    
    nonemulously
    
    nonequivocating
    
    nonestimableness
    
    noneuphonious
    
    nonexternalized
    
    nonextrusive
    
    nonfestiveness
    
    nonformidability
    
    nonfrequently
    
    nonimperialistic
    
    nonindulgently
    
    noninfiniteness
    
    noninheritabness
    
    nonintersecting
    
    noniridescently
    
    nonleprously
    
    nonmanipulative
    
    nonmathematically
    
    nonmischievous
    
    nonnarcissistic
    
    nonoutlawries
    
    nonparticipating
    
    nonpermanently
    
    nonpersecutive
    
    nonperseverant
    
    nonphilosophy
    
    nonphilosophical
    
    nonpoisonous
    
    nonpreciously
    
    nonpredatorily
    
    nonprotesting
    
    nonpsychopathic
    
    nonpuerilities
    
    nonrecuperative
    
    nonrehabilitation
    
    nonrenunciation
    
    nonresonantly
    
    nonretrenchment
    
    nonromantically
    
    nonsanctification
    
    nonsculptural
    
    nonsecretively
    
    nonsensibility
    
    nonsensification
    
    nonsentiently
    
    nonseriously
    
    nonsolubleness
    
    nontemperamental
    
    nontheistically
    
    nontypicalness
    
    nonubiquitary
    
    nonupholstered
    
    nonusurious
    
    nonvulgarities
    
    normalizations
    
    octophthalmous
    
    oligophyllous
    
    omniproduction
    
    operationalistic
    
    ophthalmencephalon
    
    ophthalmoscopy
    
    ophthalmoscopical
    
    opinionatively
    
    optimizations
    
    ornithopteris
    
    orthodoxality
    
    orthotropism
    
    ossiculotomy
    
    ostensibilities
    
    osteomyelitis
    
    ostreiculture
    
    ovariectomizing
    
    overaccentuation
    
    overbashfulness
    
    overcaustically
    
    overcommendation
    
    overcourtesy
    
    overdefensively
    
    overfearfulness
    
    overgesticulated
    
    overjoyfully
    
    overliveliness
    
    overnarrowly
    
    overnationalize
    
    overnumerous
    
    overroughness
    
    oversensitize
    
    overslowness
    
    overstrictly
    
    overthrowers
    
    overtruthful
    
    overvigorous
    
    palaeodictyoptera
    
    paleoethnography
    
    paleomammologist
    
    parametrization
    
    parasigmatismus
    
    parentheticality
    
    participatively
    
    patronizingly
    
    penetratingness
    
    perityphlitis
    
    permittivity
    
    perniciousness
    
    persnicketiness
    
    petrographically
    
    petromyzonidae
    
    petrosiliceous
    
    phylloptosis
    
    physicochemically
    
    phytogeography
    
    phytogeographical
    
    photographically
    
    photoptometer
    
    photosynthate
    
    phthisiologist
    
    pyrenocarpous
    
    pyroantimonate
    
    pyrophorous
    
    pyrotechnically
    
    pleocrystalline
    
    pleurosaurus
    
    plumbosolvent
    
    plurivorous
    
    pneumatogenous
    
    pneumatometry
    
    pneumonotomy
    
    poecilocyttares
    
    pointlessness
    
    polariscopically
    
    polychromatize
    
    polyembryonate
    
    popularisation
    
    posterishness
    
    posteruptive
    
    posteternity
    
    postexistent
    
    posttreatment
    
    pratiyasamutpada
    
    preacknowledgment
    
    preassumption
    
    precorruptive
    
    predesirously
    
    predetermination
    
    preindependently
    
    preintercourse
    
    preprocessors
    
    prereconciliation
    
    presymphysial
    
    presuitability
    
    presupplicating
    
    preteressential
    
    preventionist
    
    prioristically
    
    proacquisition
    
    proauthority
    
    procompulsion
    
    prodeportation
    
    professionalised
    
    professionally
    
    profitmongering
    
    progeotropism
    
    prognostically
    
    prohostility
    
    proletarianness
    
    propolization
    
    proportionated
    
    proportioning
    
    protomagnesium
    
    protomeristem
    
    protorosauria
    
    protosulphate
    
    prototrophy
    
    protractility
    
    providentialism
    
    proximolingual
    
    pseudodipteros
    
    pseudofoliaceous
    
    pseudonymity
    
    pseudopermanent
    
    pseudosophist
    
    psychiatrists
    
    psychoanalysis
    
    pterylography
    
    pterylographical
    
    pteroclomorphic
    
    pumpkinification
    
    pupilloscoptic
    
    purposefully
    
    pussyfooting
    
    quadricentennials
    
    quarterstaves
    
    querulously
    
    questionnaires
    
    quinquelocular
    
    quinquepetaloid
    
    quinquevalency
    
    rapturously
    
    reassortments
    
    recollectiveness
    
    reconstructing
    
    reconsultation
    
    refamiliarization
    
    reformulations
    
    reharmonization
    
    rehypnotizing
    
    relentlessness
    
    repetitiveness
    
    repressibility
    
    repressionist
    
    reproducibility
    
    requisitioning
    
    restaurateurs
    
    retributively
    
    reverentialness
    
    rhinocerotiform
    
    rhythmization
    
    rhodospermous
    
    rontgenoscopy
    
    rumblegumption
    
    saponaceousness
    
    satisfyingness
    
    scintillatingly
    
    sclerotization
    
    scrofulously
    
    scutelligerous
    
    sedimentologist
    
    semianatropous
    
    semimembranosus
    
    seminuliferous
    
    semiphenomenally
    
    semipictorially
    
    semipropagandist
    
    semitendinosus
    
    sentimentality
    
    shirtlessness
    
    sympatholytic
    
    syphilologist
    
    siphonognathus
    
    slaveownership
    
    snippersnapper
    
    sparrowwort
    
    spermatocystic
    
    spermatogonium
    
    spermophorium
    
    sphygmographies
    
    splanchnopleuric
    
    sporomycosis
    
    sputteringly
    
    squamotemporal
    
    stereoisomerical
    
    stylistically
    
    stillatitious
    
    stylomandibular
    
    stylotypite
    
    strawberrylike
    
    stroboradiograph
    
    subantiqueness
    
    subantiquities
    
    subassociations
    
    subcompensation
    
    subconjunctival
    
    subcontiguous
    
    subcorporation
    
    subdirectorship
    
    subexpression
    
    subhypothesis
    
    subnaturalness
    
    subpartnership
    
    subpharyngeally
    
    substantialist
    
    sulphurosyl
    
    sunburntness
    
    superacuteness
    
    superconsecrated
    
    superfecundity
    
    supergravitated
    
    superinfusion
    
    supermilitary
    
    superocularly
    
    superponderant
    
    superprinting
    
    superproducing
    
    superreflection
    
    supportress
    
    suppositional
    
    suprahumanity
    
    supraocclusion
    
    suspensively
    
    sussultorial
    
    telegraphonograph
    
    tendenciousness
    
    terminalization
    
    terpsichoreally
    
    terrestrialism
    
    territorialism
    
    tetrapneumones
    
    tetrapneumonian
    
    thelyotokous
    
    theomythologer
    
    theriomorphism
    
    thermotelephonic
    
    thymolphthalein
    
    thyrocalcitonin
    
    thyrotrophin
    
    thirtytwomo
    
    thoracomyodynia
    
    tylosteresis
    
    tyroglyphus
    
    tithymalopsis
    
    tocodynamometer
    
    topochemistry
    
    toponeurosis
    
    torrentiality
    
    torrentuous
    
    tortuously
    
    toxophorous
    
    transitivity
    
    transparentize
    
    transversally
    
    trichoepithelioma
    
    trimeresurus
    
    trinitrocarbolic
    
    trypanosomacidal
    
    trophoplasmatic
    
    tubercularizing
    
    ultrabenevolent
    
    ultrabrachycephalic
    
    ultrainclusive
    
    unattestedness
    
    unattributably
    
    unaudaciousness
    
    unauthentically
    
    unavertibleness
    
    unbenevolently
    
    uncalamitously
    
    uncapriciously
    
    uncategoricalness
    
    uncompromising
    
    uncondensableness
    
    uncongressional
    
    uncontemningly
    
    uncontinently
    
    uncontinuous
    
    uncontortedly
    
    uncrossableness
    
    underconstumble
    
    undergoverness
    
    undeteriorative
    
    undetestability
    
    undisinterested
    
    undispassionate
    
    undistortedly
    
    unembellishedness
    
    unexceptionably
    
    unexpectability
    
    unexplanatory
    
    unexpropriated
    
    ungratuitous
    
    unhistorically
    
    unhumourous
    
    uninvigorative
    
    uninvolvement
    
    unirritableness
    
    universalizing
    
    universitatis
    
    unjournalistic
    
    unlibidinously
    
    unmunificently
    
    unnervously
    
    unorientalness
    
    unorthographical
    
    unperemptory
    
    unphysiological
    
    unplutocratical
    
    unprecipitous
    
    unpredisposing
    
    unprotrudent
    
    unputatively
    
    unreluctantly
    
    unremunerative
    
    unreproachingly
    
    unsecretarylike
    
    unsensitizing
    
    unsensualistic
    
    unstealthiness
    
    unstimulative
    
    unstorminess
    
    unsubstantiate
    
    unsufficingness
    
    unsusceptibly
    
    unsuspicious
    
    untenantableness
    
    unterminational
    
    unulcerously
    
    unundulatory
    
    unveritableness
    
    unvictorious
    
    unwatchfulness
    
    uredosporous
    
    ureterolysis
    
    vapourishness
    
    vasoinhibitory
    
    vasostimulant
    
    ventrifixation
    
    ventriloquise
    
    ventripotency
    
    violoncellists
    
    virtuosities
    
    viscometrically
    
    vitreousness
    
    whitlowwort
    
    wondrousness
    
    worshipability
    
    zeuctocoelomatic
    
    zygapophysis
    
    

嗯？怎么输出结果跟想得不一样？找到的词怎么都 “奇形怪状” 的…… 而且，输出结果中也没有 `attitude` 这个词。

插入个中止语句，`break`，把找到的第一个词中的每个字符和它所对应的值都拿出来看看？


```python
def sum_of_word(word):
    sum = 0
    for char in word:
        sum += ord(char) - 96
    return sum

with open('words_alpha.txt', 'r') as file:
    for word in file.readlines():
        if sum_of_word(word) == 100:
            print(word)
            for c in word:        # 把字母和值都打出来，看看对不对？
                print(c, ord(c) - 96)
            break                 # 找到一个之后就停下来。
```

    abstrusenesses
    
    a 1
    b 2
    s 19
    t 20
    r 18
    u 21
    s 19
    e 5
    n 14
    e 5
    s 19
    s 19
    e 5
    s 19
    
     -86
    

怎么有个 `-86`？！仔细看看输出结果，看到每一行之间都被插入了一个空行，想到应该是从文件里读出的行中，包含 `\n` 这种换行符…… 如果是那样的话，那么 `ord('\n') -96` 返回的结果是 `-86` 呢，怪不得找到的词都 “奇形怪状” 的……


```python
ord('\n') -96
```




    -86



改进一下呗 —— 倒也简单，在计算前把读入字符串前后的空白字符都给删掉就好了，用 `str.strip()` 就可以了：


```python
def sum_of_word(word):
    sum = 0
    for char in word:
        sum += ord(char) - 96
    return sum

with open('words_alpha.txt', 'r') as file:
    for word in file.readlines():
        if sum_of_word(word.strip()) == 100:
            print(word)
```

    abactinally
    
    abatements
    
    abbreviatable
    
    abettors
    
    abomasusi
    
    abreption
    
    abrogative
    
    absconders
    
    absinthol
    
    absorbancy
    
    acceptavit
    
    acceptors
    
    acclimation
    
    accounter
    
    accumulate
    
    acenaphthene
    
    achronism
    
    achroous
    
    acylation
    
    acknowledge
    
    acolytes
    
    acquisita
    
    acquitted
    
    acriflavine
    
    acromegaly
    
    acronychal
    
    acronycta
    
    acronyx
    
    actinocarp
    
    activates
    
    acuminose
    
    acurative
    
    addressing
    
    adelocodonic
    
    ademonist
    
    adiabatically
    
    adipopexia
    
    adsessor
    
    adulthood
    
    advantaging
    
    adventual
    
    adverting
    
    aeolipyle
    
    aequorin
    
    aeriality
    
    aerofoils
    
    aerometer
    
    aetosaur
    
    affectation
    
    affricative
    
    afghanistan
    
    africanist
    
    aftercareer
    
    agalactous
    
    agamogenetic
    
    agapornis
    
    agariciform
    
    aggresses
    
    agnations
    
    agrypniai
    
    agrology
    
    agrological
    
    aichmophobia
    
    aydendron
    
    airdrops
    
    airmonger
    
    aistopoda
    
    albedometer
    
    albuminoid
    
    alchemising
    
    alertest
    
    aleurodes
    
    alfaquins
    
    algaeology
    
    algaeological
    
    alienation
    
    alineation
    
    aliturgic
    
    alkalinize
    
    alkoxyl
    
    allenarly
    
    allentato
    
    alligation
    
    alloquial
    
    allottable
    
    allthorn
    
    almoravide
    
    alopecist
    
    alphonso
    
    alpinery
    
    alpinist
    
    alrighty
    
    altarist
    
    alternated
    
    altiplano
    
    altiscope
    
    amanitins
    
    amarillos
    
    ambulating
    
    amelcorns
    
    ameloblast
    
    ametropic
    
    amiableness
    
    amyluria
    
    ammiaceous
    
    amoebobacter
    
    amoralize
    
    amortise
    
    amphiboles
    
    amphisbaenic
    
    amphiumidae
    
    amputees
    
    amusedly
    
    anacamptics
    
    analysis
    
    anapodeictic
    
    anastaltic
    
    anchoritic
    
    andesinite
    
    androclinia
    
    anepiploic
    
    aneurism
    
    angelologic
    
    angerless
    
    angiectopia
    
    anginous
    
    angioblast
    
    angiotribe
    
    angiport
    
    anglemeter
    
    anglophobia
    
    angulates
    
    anhydrated
    
    anhydremic
    
    anilinism
    
    animately
    
    animaters
    
    anisocercal
    
    annapurna
    
    annually
    
    annullate
    
    anomalipod
    
    anomalure
    
    anophelinae
    
    anorchism
    
    answerable
    
    antalkaline
    
    antalkalis
    
    antarctalia
    
    antepaschal
    
    anteporch
    
    anterior
    
    anthozoa
    
    anticathode
    
    antichlor
    
    anticomet
    
    anticult
    
    antifowl
    
    antigalactic
    
    antimarian
    
    antislip
    
    anvilling
    
    apheresis
    
    aphrodisia
    
    apiology
    
    apneumona
    
    apoharmine
    
    apokreos
    
    apoplectic
    
    aporetical
    
    apostacy
    
    apotracheal
    
    appeasers
    
    apperceive
    
    appertain
    
    applicancy
    
    appliedly
    
    applying
    
    appointed
    
    appraisable
    
    apropos
    
    aquamarine
    
    aquiform
    
    araneiform
    
    arbitrages
    
    arbuscles
    
    archaeolith
    
    archaicness
    
    archdiocesan
    
    archenemies
    
    archibenthic
    
    archigony
    
    archilithic
    
    archimagus
    
    archiplasm
    
    archsewer
    
    arcticward
    
    arenilitic
    
    areometer
    
    argillitic
    
    argiopoidea
    
    argumenta
    
    arhatship
    
    aryanism
    
    arightly
    
    arrastre
    
    arrests
    
    arrivals
    
    arrowy
    
    arsenium
    
    artilize
    
    arugulas
    
    asbestos
    
    ascendants
    
    ascyrum
    
    ascophore
    
    asyndetic
    
    asparagyl
    
    aspergilla
    
    asphalter
    
    asplanchnic
    
    assafoetida
    
    asswaging
    
    asterales
    
    asterioid
    
    asthenies
    
    atheromas
    
    athetosic
    
    athyris
    
    athyroid
    
    athreptic
    
    athrogenic
    
    atonally
    
    attargul
    
    attitude
    
    attunes
    
    auctorial
    
    audiophile
    
    augments
    
    aulophobia
    
    auntlier
    
    aureoline
    
    aureous
    
    auriculo
    
    auriscalp
    
    auslaute
    
    autoclave
    
    autoharp
    
    automated
    
    avanters
    
    avernus
    
    aversant
    
    avidious
    
    avocation
    
    avouching
    
    awfully
    
    aworry
    
    azurite
    
    babesiosis
    
    bacilliform
    
    backslashes
    
    backswept
    
    backtracking
    
    backwardly
    
    baconianism
    
    baculiform
    
    baguettes
    
    baldnesses
    
    balistarii
    
    balletomane
    
    ballonets
    
    bananaquit
    
    bandwagons
    
    bannerols
    
    barbarising
    
    bardolphian
    
    bariatrics
    
    barytone
    
    barkeepers
    
    barkpeeling
    
    barleybrake
    
    barleybreak
    
    barometz
    
    baronetical
    
    barracouta
    
    barspoon
    
    bartizaned
    
    bartonella
    
    basidorsal
    
    basilateral
    
    basiotribe
    
    basipodite
    
    bassanello
    
    bassetite
    
    bastinading
    
    bathyscape
    
    batidaceous
    
    batonist
    
    batrachoididae
    
    battailant
    
    battement
    
    baulkiest
    
    bawsunt
    
    beautihood
    
    becarpeting
    
    becrowding
    
    bedazzles
    
    bedposts
    
    beeftongue
    
    beestings
    
    beetroot
    
    beginnings
    
    beguileful
    
    belinuridae
    
    bellwaver
    
    bemajesty
    
    benediction
    
    benzolate
    
    bergamots
    
    beryllate
    
    berlinize
    
    beshrivel
    
    besmircher
    
    bespangles
    
    bespreading
    
    bestirred
    
    bestridden
    
    beswarms
    
    betattered
    
    bettering
    
    bevellers
    
    bewhisker
    
    bewitching
    
    biathlons
    
    bibitory
    
    bibliophobia
    
    bibliotics
    
    biddulphiaceae
    
    bifarious
    
    bigaroons
    
    bikukulla
    
    bilifaction
    
    bilocation
    
    biloculate
    
    bimillennia
    
    bimorphs
    
    bintangor
    
    bioassayed
    
    biologize
    
    biophyte
    
    biovular
    
    bipartite
    
    byplays
    
    birthmark
    
    bisantler
    
    biscutate
    
    bisinuate
    
    bismarckian
    
    bissonata
    
    bittings
    
    biunity
    
    biweeklies
    
    biwinter
    
    blackfriars
    
    blarneyer
    
    blasphemes
    
    blastoffs
    
    blazoning
    
    blennogenic
    
    blethering
    
    blighters
    
    blissful
    
    blockwood
    
    bloodwit
    
    blowtube
    
    bluegums
    
    boatloading
    
    bodyplate
    
    bogberries
    
    bogwoods
    
    bogwort
    
    boycott
    
    boilerful
    
    bolstered
    
    bombacaceous
    
    bonhomies
    
    bonneting
    
    boobyism
    
    boohooing
    
    boomless
    
    boondoggled
    
    bootblacks
    
    bootery
    
    bootmaker
    
    boozers
    
    bordroom
    
    borneols
    
    borrowed
    
    boskiest
    
    bosporan
    
    botanist
    
    bouillon
    
    boulevard
    
    bounceably
    
    boundary
    
    boundure
    
    bountree
    
    boviform
    
    bowerlet
    
    bowerly
    
    bowerlike
    
    bowknot
    
    bowlmaker
    
    boxboards
    
    brachiation
    
    bractlets
    
    brahmaness
    
    braquemard
    
    brawlys
    
    breakshugh
    
    breathily
    
    breediness
    
    breezeful
    
    bremeness
    
    brevetcy
    
    breviary
    
    breviconic
    
    brewises
    
    brezhnev
    
    bridgeless
    
    bridgemaking
    
    brigatry
    
    brightish
    
    bromauric
    
    bronchocele
    
    bronchus
    
    bronzy
    
    broodily
    
    brooklime
    
    broomweed
    
    browser
    
    browsick
    
    bruiting
    
    brushier
    
    brushmen
    
    bufotalin
    
    bugproof
    
    bulgurs
    
    bulliest
    
    bullnose
    
    bullpup
    
    bullskin
    
    bumblekite
    
    burnished
    
    bushlands
    
    busticate
    
    butcherer
    
    buzzy
    
    cacidrosis
    
    cacogenesis
    
    cacomistle
    
    cacophony
    
    cacophonical
    
    caecotomy
    
    calaminaris
    
    calciphyre
    
    calcitonin
    
    calculist
    
    caligraphy
    
    calypter
    
    callitriche
    
    calvarium
    
    calvities
    
    campanular
    
    camphorate
    
    cancriform
    
    candidature
    
    canephoroe
    
    cannibalized
    
    cantrips
    
    capiteaux
    
    capitolian
    
    caponiers
    
    caponiser
    
    caponniere
    
    captaincies
    
    carbazylic
    
    carboluria
    
    carboxyl
    
    carburised
    
    carcharodon
    
    cardiograph
    
    cardioplegia
    
    cardiorenal
    
    caressant
    
    caryatids
    
    carkingly
    
    carlyleian
    
    carlylese
    
    carotenes
    
    carouser
    
    carpenter
    
    carpetweed
    
    carrioles
    
    carroty
    
    cartelism
    
    cashdrawer
    
    cassalty
    
    cassiopeid
    
    castores
    
    catalanist
    
    catamneses
    
    cataphracted
    
    cataphracti
    
    catarinite
    
    catechisms
    
    catechistic
    
    catenative
    
    catholicon
    
    catteries
    
    catwort
    
    causeries
    
    cavernlike
    
    cavitates
    
    celibatist
    
    cellarway
    
    cellfalcicula
    
    celticist
    
    celtophil
    
    cencerros
    
    cenogenetic
    
    censorate
    
    censurable
    
    centipedes
    
    centupled
    
    cephalous
    
    ceramicist
    
    ceratites
    
    ceratomania
    
    cerithioid
    
    cerusite
    
    chaenomeles
    
    chaetosoma
    
    chaiseless
    
    chamecephaly
    
    champignon
    
    chaplaincies
    
    charwomen
    
    chastening
    
    chattery
    
    chaucerism
    
    chaussees
    
    cheesemaking
    
    chemigraphic
    
    chemotactic
    
    chemurgy
    
    chemurgical
    
    cherishing
    
    chickories
    
    chiliasts
    
    chimpanzee
    
    chippewas
    
    chiropodic
    
    chirpily
    
    chivariing
    
    chloranaemia
    
    chlorellaceae
    
    chloropal
    
    choiceness
    
    chondrioma
    
    chondrule
    
    choppers
    
    chorioids
    
    chorisis
    
    chortles
    
    chowries
    
    chrysalida
    
    chromium
    
    chucklers
    
    churchful
    
    churingas
    
    chutist
    
    cyaphenine
    
    cyathium
    
    cycadaceous
    
    cicatricula
    
    cyclitis
    
    cilicious
    
    cymation
    
    cymophane
    
    cinchotine
    
    cingulum
    
    cyniatria
    
    circleting
    
    circumduce
    
    cirurgian
    
    cisjurane
    
    cisleithan
    
    cysteine
    
    cystidean
    
    civilizade
    
    civilizee
    
    claytonia
    
    clangoring
    
    clangoured
    
    clarifiers
    
    classily
    
    claudetite
    
    clausure
    
    cleanliest
    
    clearhearted
    
    clementine
    
    clerically
    
    clerkdoms
    
    cleveites
    
    climatarchic
    
    clinically
    
    clockwise
    
    clomiphene
    
    clotting
    
    clovery
    
    clubster
    
    clumsier
    
    coannexes
    
    coarsest
    
    coassert
    
    coassumed
    
    coasters
    
    coatroom
    
    coattails
    
    coauthered
    
    cobleskill
    
    cobwebbery
    
    cockneyfied
    
    cockshut
    
    cocottes
    
    coderives
    
    coenures
    
    cofactors
    
    cognatus
    
    cogredient
    
    coyness
    
    coislander
    
    cojuror
    
    colascioni
    
    colazione
    
    colleagues
    
    collecting
    
    colloque
    
    colonials
    
    colopexia
    
    colophenic
    
    colubrinae
    
    columbite
    
    columels
    
    cometary
    
    commandeered
    
    commercing
    
    companero
    
    companion
    
    compering
    
    competible
    
    complanate
    
    complicacy
    
    comport
    
    concavely
    
    conchitis
    
    concludible
    
    condensate
    
    confabulate
    
    confederated
    
    confrater
    
    congeners
    
    congenital
    
    congiaries
    
    congress
    
    conjoint
    
    conjugated
    
    conjunct
    
    connivance
    
    conniver
    
    conodont
    
    consigns
    
    consumo
    
    contented
    
    contoise
    
    contrude
    
    cooingly
    
    cookeries
    
    cookout
    
    cooniest
    
    coonskin
    
    coonties
    
    coparceny
    
    copyism
    
    coplots
    
    coproduce
    
    coprosma
    
    coquets
    
    coquito
    
    corbeilles
    
    coresidence
    
    corinthiac
    
    coryphaei
    
    coryzal
    
    corkiest
    
    corkwing
    
    cornette
    
    cornmeals
    
    cornuted
    
    corollet
    
    corollike
    
    coromandel
    
    corridor
    
    corticole
    
    cosmetical
    
    cossets
    
    costumed
    
    cotabulate
    
    cotenancy
    
    cotillon
    
    cotters
    
    couchette
    
    coulombs
    
    courses
    
    courter
    
    courtin
    
    cousins
    
    covalency
    
    coverlet
    
    coverside
    
    coverup
    
    cowardish
    
    coxcomby
    
    coxcombical
    
    crackleware
    
    craniomalacia
    
    crankiest
    
    craspedum
    
    cravenly
    
    crebrity
    
    credentialed
    
    creepiest
    
    creosote
    
    crepehanger
    
    crepeiest
    
    cresoline
    
    cryalgesia
    
    crimison
    
    crimsoned
    
    criticule
    
    crowdweed
    
    crudity
    
    cruising
    
    cruisken
    
    crummier
    
    crusados
    
    crusts
    
    cuadrillas
    
    cubicities
    
    cuculus
    
    culicoides
    
    cullises
    
    cultrate
    
    culture
    
    culvers
    
    cumulated
    
    curcuddoch
    
    curdlers
    
    curettage
    
    curioso
    
    curledly
    
    curlily
    
    curtalax
    
    cuspidine
    
    customed
    
    cutdown
    
    cutesier
    
    cutinise
    
    cutlases
    
    cutlets
    
    cutlips
    
    cutout
    
    cuttles
    
    cutups
    
    czarship
    
    dacryuria
    
    daywrit
    
    danewort
    
    dartrose
    
    dawsoniaceae
    
    deadeningly
    
    deadworks
    
    deathshot
    
    debamboozle
    
    debarkation
    
    debaucheries
    
    debordment
    
    debussing
    
    decalcomanias
    
    decerebrize
    
    deciduity
    
    declarative
    
    declension
    
    decompiler
    
    decorous
    
    decouples
    
    decremental
    
    decrypted
    
    deepfroze
    
    deerberry
    
    deescalating
    
    defections
    
    defeminized
    
    deferrized
    
    definitise
    
    definitor
    
    deflators
    
    deflexure
    
    degausses
    
    deglaciation
    
    degreewise
    
    dehorting
    
    deywoman
    
    delectating
    
    deliberates
    
    delineating
    
    deliquesce
    
    delivery
    
    delouses
    
    deltation
    
    demibuckram
    
    demigriffin
    
    demisuit
    
    demivolt
    
    demobilize
    
    demodulate
    
    demoniast
    
    dempster
    
    denasalized
    
    dendrocoele
    
    denitrated
    
    denominate
    
    denounces
    
    dentalized
    
    denumerable
    
    denunciated
    
    dephycercal
    
    dephlegmated
    
    depilator
    
    deplaster
    
    depletion
    
    deploring
    
    deprivate
    
    derbylite
    
    deresinate
    
    derivers
    
    derogating
    
    derrickmen
    
    describably
    
    descriers
    
    desegregated
    
    desiccative
    
    designers
    
    desmidiales
    
    desmodus
    
    desolates
    
    despatches
    
    desponder
    
    despotat
    
    destuffs
    
    deterring
    
    developpe
    
    dewberry
    
    diagonally
    
    diakinesis
    
    dialysing
    
    dialyzer
    
    diamondize
    
    diaphyseal
    
    diaphonies
    
    diaschisis
    
    dichotomal
    
    dichromasia
    
    differencing
    
    digestive
    
    digladiator
    
    dihexagonal
    
    dykereeve
    
    dilatants
    
    dilatator
    
    dimensive
    
    dimethoate
    
    dinginess
    
    dinitrate
    
    dinitrile
    
    dioctahedral
    
    diodontidae
    
    dioecious
    
    diopsides
    
    diphygenic
    
    diphtheric
    
    diplodus
    
    diplomaing
    
    diplotene
    
    disarranged
    
    discanting
    
    discernible
    
    discipline
    
    disclaiming
    
    discommode
    
    discophile
    
    discredited
    
    discrepate
    
    disembogue
    
    disgavelled
    
    disgress
    
    dishelming
    
    dishouse
    
    disimpark
    
    disjecting
    
    disjoinable
    
    disjoint
    
    disjunct
    
    dyslectic
    
    dislodging
    
    dismarket
    
    disobliger
    
    disomus
    
    disparageable
    
    disparple
    
    dispeller
    
    dysphemia
    
    disponer
    
    dispraise
    
    disroot
    
    disrump
    
    disseized
    
    dissuader
    
    distancing
    
    distrait
    
    disunified
    
    ditchdown
    
    dithyramb
    
    ditroite
    
    diureses
    
    diurons
    
    divekeeper
    
    divertila
    
    divinely
    
    diviners
    
    divorcees
    
    dochmiasis
    
    dockyards
    
    dogmatize
    
    dogwinkle
    
    dollymen
    
    dolomitic
    
    domiciliate
    
    dominates
    
    dooryard
    
    doormaker
    
    dormette
    
    dormeuse
    
    dorosoma
    
    dorsigrade
    
    doubleheader
    
    doughty
    
    downcut
    
    downlier
    
    downset
    
    dowsabels
    
    draftsmen
    
    dragonism
    
    drawlers
    
    drawplate
    
    drybrained
    
    dryrot
    
    driveling
    
    drizzle
    
    droopier
    
    droshky
    
    droskies
    
    drossy
    
    drumbling
    
    drumlier
    
    druttle
    
    dualities
    
    duckhearted
    
    ducklings
    
    ducktails
    
    duetting
    
    dulcimore
    
    dumbfound
    
    dumpily
    
    duologue
    
    durative
    
    durdenite
    
    durions
    
    duvetine
    
    earthmaker
    
    earthwall
    
    earwigging
    
    earwort
    
    ebullient
    
    ecchymosed
    
    echiniform
    
    echinoderidae
    
    echopraxia
    
    eclecticize
    
    ecstasies
    
    ectosteal
    
    educatedly
    
    effectless
    
    efoliolate
    
    egoistical
    
    eyeleteer
    
    eightsmen
    
    ejections
    
    elaeothesia
    
    elargement
    
    eldmother
    
    electives
    
    electorial
    
    elementate
    
    elephantidae
    
    elephants
    
    elevenfold
    
    elohistic
    
    elotillo
    
    elsewhere
    
    emasculate
    
    embodiment
    
    emboldening
    
    embolismic
    
    embowers
    
    emendation
    
    emotioned
    
    empathetic
    
    emperess
    
    empresse
    
    emptily
    
    enaluron
    
    encapsuled
    
    encarpium
    
    enclasping
    
    encrust
    
    encumbers
    
    endochrome
    
    endocritic
    
    endoplasma
    
    endostraca
    
    endothecate
    
    energiser
    
    enflagellate
    
    enfoulder
    
    engagedness
    
    enharmonic
    
    enlivens
    
    enneadianome
    
    enoplion
    
    enrolles
    
    ensorceled
    
    enstyle
    
    enstool
    
    ensurance
    
    ensurer
    
    entohyal
    
    entrains
    
    envoys
    
    enweaving
    
    epibolism
    
    epicostal
    
    epigaster
    
    epigonos
    
    epilimnial
    
    epimerite
    
    epimerum
    
    episcleral
    
    episematic
    
    epistlar
    
    epitaphical
    
    epitaxy
    
    epizoon
    
    eponymic
    
    equalized
    
    equative
    
    equipages
    
    equiradical
    
    equison
    
    equispaced
    
    erasement
    
    erasions
    
    erubescence
    
    espouse
    
    esprove
    
    essoins
    
    esterified
    
    estheses
    
    esthesio
    
    estopped
    
    estuant
    
    eteoclus
    
    ethanoyl
    
    etherized
    
    ethylated
    
    ethnicism
    
    ethnogenic
    
    eucrites
    
    eugenist
    
    eulogize
    
    eunuchoid
    
    eupatridae
    
    euphenics
    
    eutony
    
    evechurr
    
    eventual
    
    everting
    
    evolute
    
    evolves
    
    exanthine
    
    excavates
    
    excellent
    
    excerpted
    
    excoriate
    
    excussed
    
    exdividend
    
    execrates
    
    exergonic
    
    exhibiter
    
    exigencies
    
    exintine
    
    exister
    
    exocyclica
    
    exophasic
    
    exoterica
    
    expalpate
    
    explains
    
    explodes
    
    exsculp
    
    exserted
    
    extendible
    
    extracted
    
    exzodiacal
    
    faculative
    
    fairyism
    
    faithwise
    
    falciparum
    
    falseness
    
    falsities
    
    fanflower
    
    fantasts
    
    fantoddish
    
    farnovian
    
    farriery
    
    farrows
    
    fasciculate
    
    fascinery
    
    fatalisms
    
    fatalistic
    
    fatherhood
    
    fatherling
    
    fattiest
    
    fauvette
    
    featherlet
    
    featherlike
    
    featherweed
    
    feldspars
    
    felinity
    
    fellowred
    
    feltness
    
    fenestral
    
    feoffeeship
    
    ferments
    
    fermillet
    
    ferrament
    
    ferryman
    
    ferrites
    
    ferrums
    
    fervanite
    
    festally
    
    fewterer
    
    fiddlerfish
    
    fierasferid
    
    filiciform
    
    filipiniana
    
    fillagreing
    
    filtering
    
    finitely
    
    firebombing
    
    firebricks
    
    firmity
    
    fishnets
    
    fishpool
    
    fissioned
    
    fissipedal
    
    fivepins
    
    flaccidities
    
    flagellates
    
    flaggingly
    
    flangeless
    
    flankwise
    
    flatlings
    
    flavanilin
    
    fleawort
    
    fleysome
    
    flyboats
    
    flichters
    
    flyness
    
    flywinch
    
    flockiest
    
    floodwall
    
    floosies
    
    fluidist
    
    fluorine
    
    fluoroid
    
    flurry
    
    fluxweed
    
    focometer
    
    foenngreek
    
    foetalism
    
    fogscoffer
    
    follying
    
    fondlings
    
    foolship
    
    footfolk
    
    footpaces
    
    footsy
    
    forbearant
    
    forebearing
    
    foredoomed
    
    forefoot
    
    foreiron
    
    forepast
    
    foreshock
    
    foresides
    
    forespeech
    
    forewarn
    
    forgiver
    
    formeret
    
    forsaking
    
    forsung
    
    fortranh
    
    forwoden
    
    forwore
    
    foujdary
    
    fountain
    
    fourbagger
    
    foveolet
    
    foziest
    
    fractural
    
    fraughts
    
    freetrader
    
    freewoman
    
    frenchily
    
    freshest
    
    freshets
    
    friendlier
    
    frigorific
    
    fringillid
    
    frisson
    
    frogeyes
    
    frolicly
    
    froughy
    
    fuehrers
    
    fugacities
    
    fulgour
    
    fulimart
    
    fumaroidal
    
    fumeless
    
    fundless
    
    furcular
    
    furless
    
    furnacemen
    
    fursemide
    
    furuncle
    
    gablatores
    
    galactagogue
    
    gallberry
    
    galleasses
    
    gallicanism
    
    galliwasp
    
    gangliomata
    
    garlandry
    
    garnishes
    
    gascoigny
    
    gasconism
    
    gasoliner
    
    gasoscope
    
    gasteralgia
    
    gauntlet
    
    gauntly
    
    gawkhammer
    
    gecarcinus
    
    gemsbucks
    
    generating
    
    genetmoil
    
    gentianella
    
    gentisate
    
    gentrices
    
    geococcyx
    
    geographer
    
    geomantical
    
    geophagism
    
    geotaxis
    
    geraniols
    
    gerenuks
    
    germanely
    
    germanhood
    
    gestening
    
    ghostland
    
    giftwrap
    
    gigartinaceae
    
    gilravager
    
    gymnogen
    
    gingerline
    
    ginkgoales
    
    ginneries
    
    gypsies
    
    gyrally
    
    girasols
    
    gyromele
    
    giustina
    
    gladatorial
    
    gladfully
    
    gladiolus
    
    glairiest
    
    glassfish
    
    glassteel
    
    glauberite
    
    glaucidium
    
    glycogenic
    
    glycolate
    
    glyconian
    
    glimpses
    
    glissando
    
    glittered
    
    glomerella
    
    glorifies
    
    glossoid
    
    glowfly
    
    glucinum
    
    glucosin
    
    gluemaking
    
    glutamate
    
    glutelin
    
    gnatcatcher
    
    gnathopod
    
    gnostical
    
    gobbledegook
    
    gobernadora
    
    gobletful
    
    goblinism
    
    godparent
    
    goldbricks
    
    goldurned
    
    golliwog
    
    goloshes
    
    goniatite
    
    gonothecal
    
    gooneys
    
    gorgonacean
    
    gorgonian
    
    goutweed
    
    governs
    
    graybeards
    
    grainsmen
    
    graithly
    
    grandaunt
    
    granogabbro
    
    granville
    
    grapeskin
    
    grappling
    
    gravamens
    
    greatening
    
    greenflies
    
    grenadiers
    
    griefless
    
    grillers
    
    grimiest
    
    grindery
    
    grivets
    
    grizelin
    
    groenendael
    
    groggily
    
    grooty
    
    groover
    
    grouper
    
    grouts
    
    grovelled
    
    growly
    
    gruelly
    
    grumpy
    
    grunzie
    
    guayaberas
    
    guaiasanol
    
    guarantied
    
    guildsman
    
    gullery
    
    gullibly
    
    gunshop
    
    gusseted
    
    guttered
    
    guttier
    
    hackbarrow
    
    haematinon
    
    haemophiliac
    
    hailproof
    
    hainberry
    
    hairgrass
    
    halakists
    
    halftones
    
    halleflinta
    
    hamesucken
    
    hammerers
    
    hammerfish
    
    hammerkop
    
    handstone
    
    haranguing
    
    harmonics
    
    harrying
    
    haruspice
    
    hastifly
    
    haustral
    
    hawknosed
    
    headclothes
    
    headhunts
    
    healthily
    
    healthward
    
    heartblood
    
    heatedness
    
    heelpost
    
    hegemonies
    
    heydeguy
    
    helianthin
    
    heliophobia
    
    hellbroth
    
    helleborin
    
    helmetlike
    
    helotize
    
    hematites
    
    hematogenic
    
    hemiablepsia
    
    hemicollin
    
    hemidactyl
    
    hemidomatic
    
    hemiekton
    
    hemiplegy
    
    hemiterata
    
    hemoglobin
    
    hemology
    
    hennebique
    
    henrietta
    
    hepatomata
    
    hepteris
    
    heraldship
    
    herbarist
    
    heritably
    
    hermetics
    
    herpetoid
    
    heterocerc
    
    hexacoralla
    
    hexarchies
    
    hexaster
    
    hyalescence
    
    hydrachnidae
    
    hydrates
    
    hydrazide
    
    hydromel
    
    hifalutin
    
    highways
    
    hyingly
    
    hylarchical
    
    hillfort
    
    hinderers
    
    hindwards
    
    hinnying
    
    hyperemia
    
    hypohemia
    
    hypopnea
    
    hypothec
    
    hypoxic
    
    hippidion
    
    hippuric
    
    hyraxes
    
    hiroshima
    
    hirsute
    
    hysons
    
    hitchhikes
    
    hlidhskjalf
    
    hobbyist
    
    hobbistical
    
    hoboisms
    
    hodoscope
    
    hoggaster
    
    hollandaise
    
    hollandite
    
    hollantide
    
    hollering
    
    holocarpic
    
    holograph
    
    homomeral
    
    honduras
    
    hondurean
    
    honeycomb
    
    honoring
    
    honoured
    
    hoofbound
    
    hookerman
    
    hooligans
    
    hooters
    
    hoppercar
    
    horation
    
    horntip
    
    horologia
    
    horseherd
    
    horsify
    
    hospitage
    
    hospital
    
    hostaging
    
    hotblooded
    
    hotelize
    
    hotness
    
    housemen
    
    houtou
    
    howlers
    
    huccatoon
    
    huehuetl
    
    humblest
    
    hungrier
    
    huronian
    
    hurrayed
    
    husbandland
    
    hussies
    
    hutzpah
    
    yahooish
    
    yalensian
    
    yankton
    
    yaourt
    
    yappers
    
    yardbirds
    
    yarners
    
    yarrow
    
    yarwhip
    
    ichorrhoea
    
    ichthyol
    
    ideagenous
    
    identifies
    
    ideologies
    
    ideologise
    
    idyllion
    
    idiogenetic
    
    idiotry
    
    idolisms
    
    idolistic
    
    yeasting
    
    yelpers
    
    ignatius
    
    ignifying
    
    iguanodon
    
    yirring
    
    illyrian
    
    illoricata
    
    illuminee
    
    illutate
    
    imbroglio
    
    immature
    
    immitigable
    
    immusical
    
    impaction
    
    impaneling
    
    impartance
    
    imparter
    
    impellor
    
    impendent
    
    imperent
    
    imported
    
    impotence
    
    impresas
    
    impressa
    
    imprest
    
    impugnable
    
    inadequacy
    
    inaneness
    
    inanities
    
    inapplicable
    
    inbreather
    
    incensive
    
    inceptor
    
    inchoating
    
    incogitance
    
    incongealable
    
    increeping
    
    incubating
    
    indetectable
    
    indicolite
    
    indignancy
    
    indirubin
    
    indologian
    
    inductees
    
    indument
    
    indurite
    
    ineconomic
    
    inefficient
    
    inexist
    
    infantive
    
    infernos
    
    inferring
    
    infidelism
    
    infixion
    
    inflation
    
    infringer
    
    infuneral
    
    ingroup
    
    ingrown
    
    inkiness
    
    innately
    
    innovate
    
    inoculate
    
    inrooted
    
    insecticide
    
    inshoot
    
    inshrined
    
    insooth
    
    inspinne
    
    instars
    
    instore
    
    intellect
    
    intelsat
    
    interbreed
    
    interfaces
    
    interfere
    
    intermat
    
    interpale
    
    interpeal
    
    interplea
    
    intertie
    
    intexine
    
    intrados
    
    intrapial
    
    intrenched
    
    introfied
    
    inulases
    
    inwound
    
    inwraps
    
    iodonium
    
    iodophor
    
    yohimbine
    
    yokeldom
    
    ioniums
    
    irideous
    
    irishly
    
    irritate
    
    isleless
    
    ismaelism
    
    ismaelitic
    
    isocephalic
    
    isocheims
    
    isochimes
    
    isolates
    
    isophasal
    
    isthmics
    
    ytterbia
    
    yukaghir
    
    yuruna
    
    iwberry
    
    jackpudding
    
    jaguarondi
    
    jailhouse
    
    japishly
    
    jarveys
    
    jasminum
    
    jatrophic
    
    javitero
    
    jawfishes
    
    jazzlike
    
    jean-christophe
    
    jessamies
    
    jillions
    
    jimberjawed
    
    jimmying
    
    jostles
    
    journaled
    
    judiciary
    
    jumblers
    
    jumbucks
    
    juncoides
    
    junkets
    
    jurassic
    
    justing
    
    juvavian
    
    kailyards
    
    keelsons
    
    keeshonden
    
    keeshonds
    
    kehilloth
    
    keyboards
    
    keyholes
    
    ketonimid
    
    keurboom
    
    kibitzer
    
    kiddushes
    
    kimeridgian
    
    kingcups
    
    kingsize
    
    kissers
    
    kytoon
    
    kleistian
    
    knappers
    
    knifeless
    
    knockoffs
    
    knothole
    
    knouts
    
    knowledged
    
    knulling
    
    kohlrabies
    
    koimesis
    
    kolinski
    
    kookery
    
    kousso
    
    krakowiak
    
    kristin
    
    kuvasz
    
    laborsome
    
    laceflower
    
    lachrymable
    
    laciniform
    
    lacquerer
    
    ladderwise
    
    lageniform
    
    lagniappes
    
    laliophobia
    
    lamarckism
    
    lambskins
    
    laminating
    
    lampstand
    
    landholding
    
    landscaping
    
    landsting
    
    languished
    
    lapulapu
    
    larcinry
    
    largeness
    
    laterigrade
    
    latinized
    
    latirus
    
    lavature
    
    lavenders
    
    lazybone
    
    lazyish
    
    lazuline
    
    lazulis
    
    leaderless
    
    leaseholds
    
    leashless
    
    leaveless
    
    leftness
    
    legatorial
    
    legumins
    
    lemmitis
    
    lennilite
    
    lepidity
    
    leucocism
    
    leucojum
    
    leucophane
    
    leveraging
    
    levigates
    
    libellist
    
    liberalism
    
    liberator
    
    libytheidae
    
    libration
    
    lyctus
    
    lievrite
    
    liferoot
    
    lifeways
    
    ligaments
    
    lightest
    
    lightning
    
    likelihood
    
    limbuses
    
    liminess
    
    limpidly
    
    lyngbyaceae
    
    lingtow
    
    lyricise
    
    lysimachia
    
    litation
    
    literato
    
    litotes
    
    lyxose
    
    localness
    
    lodicules
    
    loftsman
    
    logicity
    
    loginess
    
    logophobia
    
    logrolled
    
    longeron
    
    longobardic
    
    longship
    
    longsome
    
    loopers
    
    loricarian
    
    loviers
    
    lowigite
    
    lowlands
    
    lowlifer
    
    lubricant
    
    luckiest
    
    lugsails
    
    lumpens
    
    lunchers
    
    luniest
    
    lunkers
    
    lupinine
    
    luteins
    
    lutraria
    
    lutrinae
    
    macabreness
    
    macartney
    
    machismos
    
    macrocosm
    
    macrograph
    
    macromazia
    
    macrozamia
    
    madreporacea
    
    madrilenian
    
    maenadically
    
    magistral
    
    magnetify
    
    magnetize
    
    mahzors
    
    maieutics
    
    mailboxes
    
    maintains
    
    makership
    
    mallophagan
    
    mamelukes
    
    mammalogy
    
    mammalogical
    
    mammutidae
    
    mandarinate
    
    mandritta
    
    mangonels
    
    mannoses
    
    mantises
    
    marauders
    
    margarins
    
    marginicidal
    
    marinates
    
    marmatite
    
    marshalcy
    
    marshalman
    
    martiloge
    
    martinet
    
    martingale
    
    maskanonge
    
    masochism
    
    masseter
    
    masticot
    
    mastoids
    
    matagory
    
    matchless
    
    matchmaking
    
    matiness
    
    matrons
    
    mattedly
    
    mattulla
    
    maximize
    
    maximus
    
    mealworm
    
    meaningly
    
    measurer
    
    medianity
    
    medicamental
    
    medusalike
    
    meetness
    
    megalensian
    
    melampus
    
    melanoderm
    
    melanous
    
    melopiano
    
    membranelle
    
    membranula
    
    meningioma
    
    menoplania
    
    menoxenia
    
    mentery
    
    mephitinae
    
    mercantile
    
    merciment
    
    mercurial
    
    merganser
    
    meridional
    
    merocrine
    
    merrily
    
    mesentera
    
    mesitite
    
    mesocranic
    
    mesohepar
    
    mesomeric
    
    mesosoma
    
    metaborate
    
    metallised
    
    metamery
    
    metanomen
    
    metaplasm
    
    metatheria
    
    metergram
    
    methanolic
    
    metregram
    
    metrized
    
    mezuzah
    
    myatonic
    
    mycoplana
    
    microbrachia
    
    microbus
    
    micromelic
    
    microseme
    
    microzoa
    
    midparent
    
    midpoint
    
    miffiness
    
    milkless
    
    millimole
    
    milwaukee
    
    mimickers
    
    mineraloid
    
    minious
    
    minitant
    
    mynpacht
    
    minuses
    
    minuter
    
    myosis
    
    myrabolam
    
    miracidium
    
    myrmicidae
    
    mirrored
    
    misatoned
    
    misbiassed
    
    misbinding
    
    miscipher
    
    miscopy
    
    miscredit
    
    misdirect
    
    miseducate
    
    misenus
    
    misguggle
    
    mislearned
    
    mismanager
    
    mismoved
    
    misogallic
    
    misparse
    
    missus
    
    mistcoat
    
    mistful
    
    miswired
    
    miswish
    
    mythos
    
    mitoses
    
    mittens
    
    mniaceous
    
    mobproof
    
    moderates
    
    modiation
    
    modulant
    
    modumite
    
    molecular
    
    mollient
    
    mollifies
    
    moneyman
    
    monetise
    
    mongolian
    
    monilioid
    
    monocline
    
    monogerm
    
    monokini
    
    monomark
    
    montaging
    
    montanin
    
    montero
    
    moodiest
    
    moosebird
    
    moralism
    
    morceaux
    
    morfrey
    
    morocota
    
    morphemic
    
    morpion
    
    moshavim
    
    moslemin
    
    motors
    
    moulten
    
    moults
    
    mourns
    
    mousier
    
    moussaka
    
    mouther
    
    mowstead
    
    mozarabian
    
    mozetta
    
    muckment
    
    mucusin
    
    mufflers
    
    muyusa
    
    muktatma
    
    mullers
    
    multiped
    
    multum
    
    mummify
    
    munnion
    
    muradiyah
    
    muraenoid
    
    murillo
    
    murkly
    
    murrey
    
    murzim
    
    muscicole
    
    mushru
    
    muskish
    
    musths
    
    mutagens
    
    mutedly
    
    mutillid
    
    nahuatlecan
    
    nayword
    
    naloxone
    
    nanocurie
    
    napellus
    
    nappiest
    
    nastily
    
    natuary
    
    nauticals
    
    necrophil
    
    necturidae
    
    needlemaking
    
    negotiated
    
    negroism
    
    neoblastic
    
    neoclassic
    
    neonomian
    
    neossine
    
    neotype
    
    nervism
    
    nesters
    
    nestling
    
    netheist
    
    neurergic
    
    neurofil
    
    neuronal
    
    newsful
    
    newstand
    
    nextly
    
    nidulus
    
    niellist
    
    niggardize
    
    niggertoe
    
    nighters
    
    nightfish
    
    nihilist
    
    nimious
    
    ninepins
    
    nitrolic
    
    nivellate
    
    nodality
    
    nonaccepted
    
    nonamendable
    
    nonanarchic
    
    nonaphasic
    
    nonblockaded
    
    nonblooded
    
    nonbreakable
    
    nonbreeder
    
    noncasual
    
    noncausal
    
    nonchokable
    
    nonciteable
    
    noncredence
    
    nondairy
    
    nondeist
    
    nonfebrile
    
    nonhuman
    
    nonplacet
    
    nonpliable
    
    nonsabbatic
    
    nonsaleable
    
    nonserif
    
    nonspecie
    
    nonteachable
    
    norites
    
    nosairian
    
    nosebleeds
    
    nosohaemia
    
    nostalgic
    
    notating
    
    notchboard
    
    notional
    
    noveldom
    
    novellas
    
    nucleates
    
    nugacity
    
    nullism
    
    numeracy
    
    nutates
    
    nutty
    
    oarfishes
    
    obligatos
    
    obligatum
    
    obliques
    
    obouracy
    
    obscenely
    
    obsidional
    
    obtests
    
    obtuser
    
    occultate
    
    oceanarium
    
    octachordal
    
    octactiniae
    
    octaeterid
    
    octameter
    
    octodecimal
    
    odontalgic
    
    oecumenian
    
    oestrin
    
    offhandedly
    
    offishly
    
    ogrisms
    
    oilheating
    
    oiltight
    
    okruzi
    
    oligochaete
    
    olonets
    
    omittance
    
    omitter
    
    omniarchs
    
    omophagy
    
    onymatic
    
    oniscus
    
    onomancy
    
    oogametes
    
    opaquing
    
    operagoer
    
    operatical
    
    operetta
    
    ophiurid
    
    opticly
    
    orality
    
    orbitelar
    
    orchestic
    
    ordures
    
    oreamnos
    
    orients
    
    ornament
    
    orphancy
    
    orthose
    
    ortygan
    
    oscillated
    
    osculated
    
    osnappar
    
    osseins
    
    ossifier
    
    osteitic
    
    osteogen
    
    ostracode
    
    otorrhea
    
    outbaking
    
    outbragged
    
    outcept
    
    outcook
    
    outcut
    
    outfawn
    
    outflank
    
    outflue
    
    outgambled
    
    outhired
    
    outkill
    
    outlier
    
    outlined
    
    outoffice
    
    outpaces
    
    outpeer
    
    outray
    
    outrank
    
    outrate
    
    outreached
    
    outscape
    
    outset
    
    outshake
    
    outslid
    
    outsped
    
    outtalk
    
    outtear
    
    ovately
    
    overably
    
    overblanch
    
    overboard
    
    overbore
    
    overbow
    
    overcapable
    
    overchased
    
    overcup
    
    overdazed
    
    overdried
    
    overfilm
    
    overflog
    
    overgamble
    
    overlash
    
    overlave
    
    overleer
    
    overline
    
    overmind
    
    overneat
    
    overrim
    
    overtoe
    
    overwake
    
    overweak
    
    ovulated
    
    owelty
    
    oxammite
    
    oxcarts
    
    oxyacids
    
    oxydasic
    
    oxygon
    
    oxtails
    
    padcluoth
    
    paedology
    
    paedological
    
    paillette
    
    paintably
    
    pairwise
    
    pakistani
    
    palaeograph
    
    palaeophile
    
    palagonite
    
    palatally
    
    palmipedes
    
    palpitate
    
    palpless
    
    paludrine
    
    panathenaean
    
    panderers
    
    pandurate
    
    panhandling
    
    pansmith
    
    pantoum
    
    papality
    
    papyrian
    
    papists
    
    parablepsia
    
    parachaplain
    
    parachutic
    
    paracmasis
    
    paracress
    
    paradeless
    
    parageusic
    
    paralleler
    
    paramecium
    
    paranuclei
    
    pararctalia
    
    parentate
    
    parleyer
    
    parousia
    
    parricidial
    
    pasteur
    
    pastiches
    
    pasture
    
    patellula
    
    pathless
    
    patinize
    
    pauraque
    
    paviors
    
    pavisor
    
    paxillate
    
    peculium
    
    pedagogues
    
    pedantize
    
    pederastic
    
    pediadontic
    
    pediculated
    
    pediculati
    
    pediculicide
    
    pedicures
    
    peirastic
    
    pelargonic
    
    pelleting
    
    penalises
    
    penchants
    
    penduline
    
    penlites
    
    penorcon
    
    penutian
    
    peoplish
    
    perborate
    
    percents
    
    perforata
    
    performed
    
    periauger
    
    peridermic
    
    perijove
    
    perilune
    
    peripherad
    
    perisome
    
    permits
    
    peroxided
    
    perscent
    
    personage
    
    personal
    
    perturb
    
    pesthole
    
    pestify
    
    pettiagua
    
    petunse
    
    phagocyte
    
    phalangitic
    
    phalanxes
    
    phantasmag
    
    pharisees
    
    phasiron
    
    phenacetine
    
    phenetole
    
    phenixes
    
    phenocoll
    
    phenolated
    
    phenoxide
    
    phycitidae
    
    physicked
    
    phlebitis
    
    phlogisma
    
    pholidota
    
    phoronida
    
    phosphide
    
    photechy
    
    photism
    
    photoeng
    
    photogen
    
    photogs
    
    photonic
    
    photopia
    
    phrynidae
    
    phulwara
    
    pianisms
    
    pianistic
    
    piblokto
    
    picarooned
    
    pictural
    
    pigeonite
    
    piggybacks
    
    pigroot
    
    pilaster
    
    pililloo
    
    pilloried
    
    pilotman
    
    pinecones
    
    piniform
    
    pintails
    
    piperidin
    
    pipkinet
    
    piquiere
    
    pyralidan
    
    pyralis
    
    pyraloid
    
    pyrexic
    
    pyridine
    
    pyritic
    
    pyrogen
    
    pishogue
    
    pisidium
    
    pistaches
    
    pistachio
    
    pistoled
    
    pitcairnia
    
    pitiedly
    
    pitying
    
    pitless
    
    pitmaking
    
    pituite
    
    pixilated
    
    placentoma
    
    placodermal
    
    placodont
    
    plagioclase
    
    playgirl
    
    playoffs
    
    plaister
    
    plaiters
    
    playward
    
    plantlet
    
    plantlike
    
    plasmagenic
    
    plastered
    
    plastids
    
    plateaux
    
    platinoid
    
    plebianism
    
    plebiscite
    
    plecotinae
    
    plenties
    
    plenums
    
    pleuroid
    
    pliskies
    
    plosion
    
    plouky
    
    plumbagine
    
    plumcot
    
    plumdamas
    
    plummet
    
    plummy
    
    plumule
    
    plunging
    
    pluries
    
    plushes
    
    plussage
    
    pneograph
    
    pocketing
    
    podicipedidae
    
    podsols
    
    podzolic
    
    poetito
    
    poetized
    
    poitrail
    
    pokomoo
    
    polyamide
    
    polygalic
    
    pollenate
    
    pollux
    
    pompilidae
    
    pondokkie
    
    ponying
    
    poorish
    
    poplitic
    
    popply
    
    poriferal
    
    portland
    
    poseuse
    
    postdate
    
    postfact
    
    posting
    
    postpaid
    
    potbellied
    
    potentee
    
    pothook
    
    potoos
    
    potpies
    
    pouchlike
    
    pounding
    
    pouring
    
    powders
    
    practicing
    
    pratty
    
    preachings
    
    preacness
    
    preadapts
    
    preatomic
    
    prebenefit
    
    precambrian
    
    precents
    
    prechordal
    
    precising
    
    preclaimer
    
    precombine
    
    preconfer
    
    predative
    
    predawns
    
    predeceases
    
    predicates
    
    preeligible
    
    preeners
    
    preentail
    
    prefeudalic
    
    preformed
    
    prelithic
    
    preludes
    
    preludio
    
    premorbid
    
    prenaris
    
    prenomen
    
    preobtain
    
    preofficial
    
    preordain
    
    prepacking
    
    prepays
    
    prerefined
    
    prereject
    
    prerelate
    
    presley
    
    presser
    
    presteel
    
    pretonic
    
    prevalue
    
    prevent
    
    priapus
    
    priestal
    
    priggess
    
    primary
    
    primeur
    
    primitiae
    
    princeps
    
    princify
    
    printer
    
    prionine
    
    prismy
    
    prisoned
    
    prittle
    
    privant
    
    proavis
    
    probings
    
    problems
    
    procaines
    
    proclive
    
    procured
    
    prodroma
    
    producer
    
    profaning
    
    profiles
    
    profundae
    
    profuse
    
    progamete
    
    progeny
    
    proleague
    
    promisee
    
    pronging
    
    prononce
    
    proofing
    
    propjet
    
    proreader
    
    prorebate
    
    prorecall
    
    proscenia
    
    prosier
    
    prosodal
    
    prosodiac
    
    prothmia
    
    proximad
    
    proxime
    
    pseudaphia
    
    psychon
    
    pteridoid
    
    ptomainic
    
    publicly
    
    pucksey
    
    puddingy
    
    pueblito
    
    puerpera
    
    pugilant
    
    pulicose
    
    pullalue
    
    pumpkin
    
    punaluan
    
    punctate
    
    pupilate
    
    purbeckian
    
    pureayn
    
    pursue
    
    purty
    
    pussy
    
    putchuk
    
    putois
    
    putter
    
    quadplex
    
    quadrangle
    
    qualmish
    
    quarry
    
    quarter
    
    quartin
    
    quassin
    
    quatrin
    
    quebrith
    
    quemeful
    
    querent
    
    quesited
    
    quicklime
    
    quietened
    
    quillais
    
    quillon
    
    quinyie
    
    quinnet
    
    quintar
    
    quints
    
    quippu
    
    quittal
    
    quomodo
    
    racemisms
    
    rachitism
    
    rackettail
    
    rackingly
    
    rackwork
    
    radarscope
    
    radiocarbon
    
    radiolitic
    
    radioteria
    
    raillery
    
    raincoats
    
    ramellose
    
    rammermen
    
    randannite
    
    rangeless
    
    rankwise
    
    ransomable
    
    rasters
    
    rastling
    
    ratatats
    
    ratchety
    
    ratheripe
    
    rattails
    
    raughty
    
    ravelers
    
    ravelins
    
    ravelling
    
    ravisher
    
    reaccelerated
    
    reaccount
    
    reaccusing
    
    reappliance
    
    reapplier
    
    rearguing
    
    rearising
    
    reattaches
    
    reattired
    
    rebozos
    
    receptor
    
    recidivated
    
    reciprocal
    
    recommendee
    
    recompete
    
    recompiled
    
    reconclude
    
    recreating
    
    rectrices
    
    redamation
    
    redeclining
    
    redeploy
    
    redesigns
    
    redespise
    
    redoubler
    
    redounds
    
    redrying
    
    redrives
    
    reduzate
    
    reeffishes
    
    reenclosed
    
    reevokes
    
    reexhibit
    
    referring
    
    refinancing
    
    refinery
    
    reflation
    
    reflexing
    
    refought
    
    refugium
    
    refuting
    
    regalement
    
    regalness
    
    regimented
    
    regionals
    
    regorging
    
    regraduate
    
    regressed
    
    regroup
    
    regrown
    
    rehammers
    
    reimpart
    
    reimpose
    
    reinjure
    
    reinvoice
    
    reissued
    
    relacquer
    
    relatival
    
    releasably
    
    remedying
    
    remeditate
    
    remigrated
    
    renegating
    
    renovate
    
    reophore
    
    repaginated
    
    repower
    
    repress
    
    reprint
    
    reprobate
    
    repugns
    
    repulsed
    
    reputable
    
    reradiates
    
    reschedule
    
    rescous
    
    researcher
    
    resecting
    
    resents
    
    reserval
    
    resewing
    
    reshipped
    
    reshoeing
    
    reshoot
    
    reshuffle
    
    residencer
    
    residues
    
    resimmer
    
    resinfiable
    
    resizer
    
    resolved
    
    respicing
    
    responded
    
    restaging
    
    restocked
    
    restore
    
    restow
    
    resumes
    
    resurfaced
    
    retaught
    
    retemper
    
    retinite
    
    retinker
    
    retinula
    
    retiring
    
    retotaled
    
    retreatal
    
    retrenched
    
    retroact
    
    retrocecal
    
    revacating
    
    reversal
    
    revibrate
    
    revives
    
    rewearing
    
    rewound
    
    rewraps
    
    rhabdosome
    
    rhagadiform
    
    rhyton
    
    ricinulei
    
    ricochets
    
    ridicules
    
    rigescent
    
    rimeless
    
    rindless
    
    ringnecks
    
    riptides
    
    rituals
    
    riverbank
    
    riverine
    
    riverman
    
    roadblocks
    
    roadster
    
    rocketeer
    
    rockiest
    
    rodentian
    
    romagnole
    
    rondelier
    
    roofward
    
    rookeried
    
    roomful
    
    roommate
    
    rootages
    
    rootier
    
    rooving
    
    rosery
    
    rosillo
    
    rosiny
    
    roubouh
    
    rouping
    
    ruction
    
    ruddiest
    
    rufous
    
    rulings
    
    rumpadder
    
    runkles
    
    runneth
    
    runtime
    
    rutelian
    
    saccharifier
    
    saddleless
    
    sagebrush
    
    sagginess
    
    saintly
    
    saintlike
    
    salacious
    
    salampore
    
    salariats
    
    saltery
    
    saltmaker
    
    salvifics
    
    samolus
    
    samshus
    
    sanitised
    
    santirs
    
    santols
    
    sanukite
    
    sapiencies
    
    sapindales
    
    sapogenin
    
    sarcolemma
    
    sarcophagic
    
    sarothra
    
    sassagum
    
    sateless
    
    satiating
    
    satinlike
    
    satirise
    
    satrapy
    
    satrapical
    
    saucepot
    
    savory
    
    saxifragaceae
    
    scabiophobia
    
    scannings
    
    scaphites
    
    scapiform
    
    scapolite
    
    scarfskin
    
    scawtite
    
    scenarize
    
    scentful
    
    schediastic
    
    schematics
    
    schiavoni
    
    schnapper
    
    scholarian
    
    scholium
    
    schoolmaam
    
    schoolman
    
    schorly
    
    sciaticky
    
    scincoidian
    
    scintler
    
    scivvy
    
    scleranth
    
    sclerosed
    
    scobiform
    
    scoreboard
    
    scorepads
    
    scotchmen
    
    scotopic
    
    scrapbook
    
    scratchman
    
    screwier
    
    scrieves
    
    scriggly
    
    scripto
    
    scrublike
    
    scrummage
    
    sculpted
    
    scumbling
    
    scuppet
    
    scurril
    
    scuttle
    
    seafowls
    
    seamster
    
    searness
    
    seashells
    
    seatless
    
    secours
    
    secreting
    
    secundum
    
    seemlily
    
    seignorage
    
    seignoral
    
    seiyukai
    
    selective
    
    selenates
    
    semaphore
    
    sementera
    
    semibarbaric
    
    semichemical
    
    semicircled
    
    semiclose
    
    semicrome
    
    semicurl
    
    semideltaic
    
    semihoral
    
    semimatt
    
    seminifical
    
    semipupa
    
    semishaft
    
    semitone
    
    senilism
    
    sennits
    
    sensoria
    
    septicemia
    
    septleva
    
    serfship
    
    serioso
    
    serjeancy
    
    serphoidea
    
    sertion
    
    sertule
    
    services
    
    sescuple
    
    session
    
    setdown
    
    setout
    
    settles
    
    setups
    
    setwise
    
    sextole
    
    shadberry
    
    shadowing
    
    shaggymane
    
    shaivism
    
    shakedown
    
    shakeout
    
    shakeups
    
    shaktism
    
    shamoys
    
    shanksman
    
    sharezer
    
    sharpens
    
    shattered
    
    shawllike
    
    sheeneys
    
    sheepwalk
    
    shellshake
    
    shelterage
    
    sheraton
    
    sheroot
    
    shibboleth
    
    shieldfern
    
    shipbuild
    
    shipcraft
    
    shipyard
    
    shiplaps
    
    shipplane
    
    shivaism
    
    shivers
    
    shlimazl
    
    shochetim
    
    shoofly
    
    shooter
    
    shopboy
    
    shortcake
    
    shovels
    
    shredders
    
    shrewder
    
    shrieking
    
    shrives
    
    syagush
    
    sicklily
    
    sycoceric
    
    sycones
    
    sienites
    
    sightly
    
    sigillarid
    
    significian
    
    signiori
    
    silicifies
    
    silicons
    
    syllabaria
    
    sillery
    
    sylviid
    
    simulate
    
    synacmy
    
    sinapism
    
    synapte
    
    sincipita
    
    syndicate
    
    synergic
    
    sinewing
    
    syntagma
    
    syphered
    
    siphons
    
    syrens
    
    siskins
    
    sissone
    
    sixtine
    
    skatings
    
    skewers
    
    skiddiest
    
    skimpier
    
    skirret
    
    skirter
    
    skittled
    
    skyugle
    
    sklents
    
    skully
    
    slangrell
    
    slaverer
    
    sleepyhead
    
    sleepry
    
    slenderer
    
    slyest
    
    slighty
    
    slithered
    
    slumps
    
    smackeroo
    
    smashboard
    
    smellful
    
    smirching
    
    smirking
    
    smitten
    
    smokelike
    
    smokers
    
    smoorich
    
    snarlish
    
    sneerful
    
    sneeshing
    
    sniffily
    
    snipelike
    
    snipers
    
    snivels
    
    snobbery
    
    snorker
    
    snubbers
    
    snuffkin
    
    sobersided
    
    socialism
    
    sociogram
    
    socrates
    
    sodiums
    
    sodomite
    
    softboard
    
    soybeans
    
    solidism
    
    solunar
    
    somites
    
    sompner
    
    sonship
    
    soonly
    
    soother
    
    sordello
    
    sorehawk
    
    sotols
    
    soughing
    
    soundheaded
    
    soupfin
    
    soupon
    
    sourball
    
    sources
    
    sourdre
    
    spacewalked
    
    spanceling
    
    sparganiaceae
    
    sparily
    
    sparkler
    
    spavins
    
    speakablies
    
    specificated
    
    speckledy
    
    speltoid
    
    spenders
    
    speranza
    
    sperling
    
    sphecius
    
    sphendone
    
    sphenion
    
    spherula
    
    sphygmic
    
    spiciest
    
    spiflicate
    
    spyhole
    
    spikehole
    
    spillages
    
    spinelet
    
    spinelike
    
    spiracula
    
    spirifer
    
    spirited
    
    spitish
    
    spizella
    
    splashy
    
    splining
    
    spoliaria
    
    spondaics
    
    spooler
    
    sporangia
    
    spouted
    
    springald
    
    springle
    
    sprucer
    
    sprugs
    
    spunks
    
    spurdog
    
    squares
    
    squaws
    
    squibbing
    
    squinched
    
    squint
    
    srikanth
    
    stabilised
    
    stagecraft
    
    staggerer
    
    stagiary
    
    stagiest
    
    stagskin
    
    staynil
    
    stalemated
    
    stalklet
    
    stalklike
    
    staminode
    
    standards
    
    standers
    
    stanzas
    
    starers
    
    starfish
    
    starling
    
    statize
    
    status
    
    steadiers
    
    steamers
    
    stearyl
    
    steckling
    
    stemming
    
    steppes
    
    steptoe
    
    stereome
    
    stiacciato
    
    stickboat
    
    styles
    
    stillman
    
    stinkball
    
    stipels
    
    stockier
    
    stockmen
    
    stokavci
    
    stomatic
    
    stonehand
    
    stonier
    
    stooges
    
    stools
    
    stoping
    
    stoves
    
    stower
    
    straddleback
    
    strains
    
    strangled
    
    strawen
    
    straws
    
    streetage
    
    stress
    
    striatal
    
    striding
    
    striker
    
    strobile
    
    strolld
    
    stroth
    
    struv
    
    stubbles
    
    stuccos
    
    stupes
    
    sturdied
    
    subadjacent
    
    subadult
    
    subalgebraic
    
    subcosta
    
    subcranial
    
    subdeliria
    
    subducing
    
    subduple
    
    subgallate
    
    subhooked
    
    subjectable
    
    subjugable
    
    sublethal
    
    sublimes
    
    subloral
    
    subnets
    
    subpool
    
    subradiate
    
    subrepand
    
    subscience
    
    subsellia
    
    subsmile
    
    subsume
    
    subucula
    
    succour
    
    sucrose
    
    sufficience
    
    suffocated
    
    suffragial
    
    sugarloaf
    
    sulfamine
    
    sulfonal
    
    sumbulic
    
    sumerian
    
    summula
    
    sunbeamy
    
    sunders
    
    sunfast
    
    sunnier
    
    sunward
    
    superceded
    
    supracaecal
    
    surcharge
    
    surely
    
    surmit
    
    suttas
    
    suzanne
    
    swampine
    
    swankier
    
    swanmark
    
    swashing
    
    swattle
    
    sweatier
    
    sweepdom
    
    sweepier
    
    sweetened
    
    sweetman
    
    sweptback
    
    swifter
    
    swimmer
    
    swinebread
    
    swingman
    
    swipple
    
    swirls
    
    swythe
    
    swollen
    
    tabernacles
    
    tablature
    
    taborets
    
    tabourer
    
    tabourin
    
    tabulary
    
    tagliarini
    
    tailcoats
    
    tailgating
    
    tailory
    
    tailspin
    
    takhtadjy
    
    tallying
    
    talocalcaneal
    
    talpetate
    
    tamehearted
    
    tanchelmian
    
    tangents
    
    tanzanian
    
    tariffize
    
    tarquin
    
    tastably
    
    tatarize
    
    tattooed
    
    taurus
    
    tautens
    
    taxeopod
    
    taxying
    
    taxless
    
    teagardeny
    
    teardown
    
    tectricial
    
    teethers
    
    telegrams
    
    telephone
    
    telescope
    
    tellinoid
    
    telluric
    
    teloptic
    
    temperance
    
    temperer
    
    templum
    
    temporal
    
    tensely
    
    tenurial
    
    teroxide
    
    terranes
    
    tersion
    
    tessaradecad
    
    tetanize
    
    tetanus
    
    tetrachical
    
    tetragon
    
    tetrahedra
    
    tetramer
    
    tetramin
    
    tetrapoda
    
    tetryl
    
    tettigidae
    
    tewsome
    
    thalidomide
    
    thaneship
    
    thegither
    
    theists
    
    theomagics
    
    theorum
    
    therapsid
    
    therebeside
    
    therefore
    
    thermels
    
    thickening
    
    thievish
    
    thinghood
    
    thynnidae
    
    thiobacilli
    
    thiolactic
    
    thiophene
    
    thioxene
    
    thirdendeal
    
    thirty
    
    thondraki
    
    thorny
    
    thrawing
    
    threatened
    
    thrifts
    
    thriver
    
    thumbnail
    
    tyburn
    
    tidemarks
    
    tiderips
    
    tiefenthal
    
    tigerism
    
    tightish
    
    tilesherd
    
    timbrelled
    
    tinkerer
    
    tinselled
    
    tipburn
    
    typefaces
    
    tipless
    
    tyramin
    
    tiredest
    
    tiremaker
    
    tiresias
    
    tironian
    
    toasty
    
    tobaccoism
    
    tobaccosim
    
    toboggans
    
    toddymen
    
    toffyman
    
    toilets
    
    toitish
    
    tolerated
    
    toluids
    
    toluole
    
    tonguer
    
    toomly
    
    tooting
    
    tornesi
    
    toroids
    
    torqued
    
    torrefied
    
    torsalo
    
    torteau
    
    tosily
    
    totty
    
    totuava
    
    tousy
    
    tovariaceae
    
    towards
    
    towers
    
    townman
    
    towser
    
    toxicaemia
    
    toxicon
    
    toxotae
    
    trachyte
    
    traction
    
    trailboard
    
    tramells
    
    tramyard
    
    trammelhead
    
    trampcock
    
    trampdom
    
    tranceful
    
    transbay
    
    transect
    
    transom
    
    travelog
    
    trecento
    
    treeship
    
    trembling
    
    tremellaceae
    
    trepangs
    
    triadist
    
    tricarbon
    
    trickful
    
    trierarch
    
    triones
    
    triplet
    
    triply
    
    tristam
    
    trisula
    
    tritor
    
    trizoic
    
    trochlear
    
    troller
    
    tropics
    
    troppo
    
    troths
    
    trotol
    
    trounced
    
    trudging
    
    truism
    
    trutta
    
    tsarevna
    
    tsktsk
    
    tsunamic
    
    tubeform
    
    tubules
    
    tularemia
    
    tummies
    
    tumoral
    
    tuneably
    
    tunnland
    
    tuppence
    
    turgite
    
    turkey
    
    turnoff
    
    turtled
    
    tussled
    
    twangler
    
    tweenies
    
    tweesht
    
    twisted
    
    twitchel
    
    twoling
    
    uigurian
    
    uitotan
    
    uitspan
    
    umbratical
    
    umpirer
    
    unaborted
    
    unabsurd
    
    unabundance
    
    unadmirable
    
    unadopted
    
    unalerted
    
    unaltered
    
    unamerceable
    
    unangry
    
    unappended
    
    unattacked
    
    unavailable
    
    unbalconied
    
    unbarreled
    
    unbarricaded
    
    unbedraggled
    
    unbeguiled
    
    unbeholden
    
    unbeloved
    
    unbendably
    
    unbenight
    
    unboring
    
    unbounded
    
    unboxes
    
    unbragging
    
    unbreast
    
    unbreeches
    
    unbridgeable
    
    unbroiled
    
    unbroken
    
    unbullied
    
    uncenter
    
    uncentre
    
    unchaining
    
    unchiseled
    
    uncialize
    
    unclever
    
    unclipped
    
    unclubby
    
    uncoaxial
    
    uncombined
    
    uncrest
    
    uncrisp
    
    unculted
    
    uncupped
    
    undamnified
    
    undeducible
    
    undeferred
    
    undelight
    
    underbalance
    
    underbodice
    
    underboil
    
    underchime
    
    underclub
    
    underdone
    
    underfiend
    
    underfire
    
    undergird
    
    underkind
    
    underlay
    
    underseam
    
    undertide
    
    undertied
    
    undowned
    
    undraws
    
    undress
    
    unduloid
    
    unelectable
    
    unenchant
    
    unequaled
    
    unevocable
    
    unfasten
    
    unfauceted
    
    unferried
    
    unfevered
    
    unfiducial
    
    unfinish
    
    unfoaming
    
    unfogging
    
    unfooted
    
    unformal
    
    unfreeze
    
    unfriended
    
    unfrugal
    
    unfuelled
    
    unfundable
    
    ungambling
    
    ungloved
    
    unhaggling
    
    unhorse
    
    unhumbled
    
    unhushed
    
    uniflow
    
    unimbibing
    
    unindexed
    
    unironed
    
    unitive
    
    unjapanned
    
    unkempt
    
    unkindred
    
    unlaving
    
    unleveled
    
    unlighted
    
    unlinks
    
    unloathed
    
    unloyal
    
    unmackly
    
    unmaligned
    
    unmanly
    
    unmanlike
    
    unmanner
    
    unmantle
    
    unmatchable
    
    unmental
    
    unmetred
    
    unminted
    
    unmiracled
    
    unmiry
    
    unmiter
    
    unmitre
    
    unmodified
    
    unmould
    
    unmown
    
    unpalpable
    
    unpapered
    
    unpasted
    
    unpegging
    
    unpitched
    
    unplashed
    
    unprimed
    
    unprince
    
    unprop
    
    unramified
    
    unrandom
    
    unrankled
    
    unreceding
    
    unrecent
    
    unrelated
    
    unrisen
    
    unrobing
    
    unrowed
    
    unsaccharic
    
    unsayable
    
    unscaling
    
    unseven
    
    unshamefaced
    
    unshelled
    
    unshocked
    
    unshore
    
    unsicker
    
    unsmeared
    
    unsnatch
    
    unsolar
    
    unstanch
    
    unstate
    
    unsteep
    
    unsunk
    
    untacking
    
    untaste
    
    untaxable
    
    untermed
    
    unthewed
    
    unthreaded
    
    untidier
    
    untipt
    
    untoiled
    
    unturf
    
    unugly
    
    unuseable
    
    unvalued
    
    unvendable
    
    unwaking
    
    unwarbled
    
    unwarned
    
    unwearied
    
    unwhite
    
    unwilled
    
    unwove
    
    upbuoy
    
    upcover
    
    upcrowd
    
    upcurled
    
    uperize
    
    upgrow
    
    uppluck
    
    upsets
    
    upspew
    
    upspread
    
    upstare
    
    uptears
    
    urbanest
    
    urbanized
    
    urceolate
    
    ureteral
    
    urobilin
    
    urohyal
    
    urologic
    
    urticaria
    
    useless
    
    uspanteca
    
    utensil
    
    uucpnet
    
    uxorial
    
    vaccinist
    
    vacuolate
    
    vacuums
    
    valbellite
    
    vallums
    
    vamosing
    
    vampyre
    
    variety
    
    varnished
    
    vasovagal
    
    vedettes
    
    vegetism
    
    vehemency
    
    velatura
    
    veliform
    
    velocipeded
    
    veloute
    
    venantes
    
    venation
    
    venditate
    
    venially
    
    ventifact
    
    ventose
    
    verbalize
    
    verbenone
    
    verbomania
    
    verdancies
    
    verdicts
    
    vergery
    
    verriere
    
    vetchling
    
    veuglaire
    
    vibists
    
    victrola
    
    vietminh
    
    vilipended
    
    villancico
    
    villanella
    
    vinagron
    
    vincular
    
    vinous
    
    violins
    
    virbius
    
    virgater
    
    visammin
    
    visionic
    
    vitameric
    
    vitaminic
    
    vitasti
    
    vitrailed
    
    vitrics
    
    vivency
    
    vivifier
    
    voyaging
    
    volleyed
    
    volvell
    
    vortical
    
    vouchsafe
    
    vulgars
    
    vulpinae
    
    wachuset
    
    walycoat
    
    wamefuls
    
    wanrest
    
    wanters
    
    warpers
    
    warragals
    
    washdays
    
    washings
    
    washway
    
    wastemen
    
    wasterie
    
    wastern
    
    watchdogs
    
    watergate
    
    wattles
    
    waveshape
    
    weariest
    
    webworn
    
    wedgewise
    
    wednesday
    
    weelfaured
    
    weeniest
    
    wehrlite
    
    wellyard
    
    wellmaker
    
    wellring
    
    whaleries
    
    wheatears
    
    wheelabrate
    
    wheelsman
    
    whelpish
    
    whenever
    
    wherves
    
    whichway
    
    whinnier
    
    whipray
    
    whirley
    
    whirling
    
    whiskey
    
    whisking
    
    whistled
    
    whitebill
    
    whitfinch
    
    wholely
    
    wholesale
    
    whooper
    
    whorish
    
    whumps
    
    widdendream
    
    widewhere
    
    wigglers
    
    wigwagger
    
    wildsome
    
    willets
    
    windfallen
    
    windfalls
    
    windigos
    
    wiredancer
    
    wirehaired
    
    wiselier
    
    wissing
    
    wistaria
    
    wistit
    
    witchlike
    
    witchweed
    
    witcraft
    
    withnay
    
    witloof
    
    witwall
    
    wizards
    
    woeness
    
    wolframic
    
    wollomai
    
    woodblock
    
    woolfell
    
    wordably
    
    worldman
    
    wormgear
    
    worset
    
    wouhleche
    
    wounder
    
    wrathing
    
    wrawler
    
    wreakers
    
    wreathy
    
    wriggles
    
    writing
    
    wronger
    
    xylose
    
    zamouse
    
    zaptiahs
    
    zaratite
    
    zesting
    
    zeuxian
    
    zincifies
    
    zinkify
    
    zithern
    
    zoogleas
    
    zorgite
    
    

如果想把符合条件的词保存到一个文件 `results.txt` 里的话，那么：


```python
def sum_of_word(word):
    sum = 0
    for char in word:
        sum += ord(char) - 96
    return sum

with open('results.txt', 'w') as result:
    with open('words_alpha.txt', 'r') as file:
        for word in file.readlines():
            if sum_of_word(word.strip()) == 100:
                result.write(word)
```

竟然这么简单就搞定了？！

这 10 行的代码，在几秒钟内从 370,101 个英文单词中找到 3,771 个如此计算等于 100 的词汇。

喝着咖啡翻一翻 `results.txt`，很快就找到了那些用来做反例格外恰当的词汇。

真无法想象当年的自己若是不懂编程的话现在会是什么样子……

## 总结

这一章我们介绍了文本文件的基本操作：

> * 打开文件，直接用内建函数，`open()`，基本模式有 `r` 和 `w`；
> * 删除文件，得调用 `os` 模块，使用 `os.remove()`，删除文件前最好确认文件确实存在……
> * 读写文件分别有 `file.read()`、`file.write()`、`file.readline()`、`file.readlines()`、`file.writelines()`；
> * 可以用 `with` 把相关操作都放入同一个语句块……
