# PAC 2 Visualització de dades

Treball de la PAC 2 de l'assignatura *Visualització de dades* (UOC), de Pau Braun Arañó. Recull tres visualitzacions interactives, una per cada tècnica assignada, amb un fil temàtic agrícola: del territori català al camp de tot el món.

| # | Tècnica | Pàgina | Què mostra |
|---|---------|--------|------------|
| 1 | Dot density map | `dot-density.html` | Distribució dels conreus llenyosos a Catalunya el 2024 (3972 punts, 1 punt = 50 ha) |
| 2 | Small multiples | `small-multiples.html` | Ús de fertilitzant (kg per hectàrea) en nou països, de 1961 a 2023 |
| 3 | Beeswarm plot | `beeswarm.html` | Rendiment de cereal (kg per hectàrea) de 174 països el 2016, agrupats per continent |

`index.html` és la pàgina d'inici, amb accés a les tres visualitzacions i als fitxers de dades.

## Estructura del repositori

```
index.html                  pàgina d'inici
dot-density.html            visualització 1
small-multiples.html        visualització 2
beeswarm.html               visualització 3
data/
  export.csv                dades filtrades del dot density
  fertilizer-use-per-hectare-of-cropland.csv   dades filtrades dels small multiples
  data.csv                  dades filtrades del beeswarm
README.md
```

Cada pàgina porta les seves dades incrustades al propi HTML, així que no depèn de cap servidor ni de cap fitxer extern, tret de la llibreria D3 (vegeu *Tecnologia*). La carpeta `data/` serveix perquè es pugui consultar i reutilitzar l'origen de dades filtrat.

## Origen de les dades

Els fitxers de `data/` conserven el **nom del fitxer original** que es va descarregar de cada font, però contenen la versió **filtrada** que fa servir cada visualització.

### 1. Conreus llenyosos de Catalunya (`export.csv`)
- **Font:** *Superfícies municipals dels cultius llenyosos a Catalunya*, dades obertes de la Generalitat de Catalunya, publicades a [datos.gob.es](https://datos.gob.es/ca/catalogo/a09002970-superficies-municipales-de-los-cultivos-lenosos-en-cataluna).
- **Període:** 2015–2024. El mapa mostra el 2024.
- **Límits municipals i comarcals:** [ArnauInes/geometries_cat_bcn_2024](https://github.com/ArnauInes/geometries_cat_bcn_2024), que parteix de dades de l'Instituto Geográfico Nacional. S'han fet servir els límits de municipis i comarques.

### 2. Fertilitzant per hectàrea (`fertilizer-use-per-hectare-of-cropland.csv`)
- **Font:** *Fertilizer use per hectare of cropland* (kg/ha), FAO, a través d'[Our World in Data](https://ourworldindata.org/grapher/fertilizer-use-per-hectare-of-cropland) (CC BY).
- **Període:** 1961–2023.

### 3. Rendiment de cereal (`data.csv`)
- **Font:** *Cereal yield (kg per hectare)*, Banc Mundial, World Development Indicators (indicador `AG.YLD.CREL.KG`), [data.worldbank.org](https://data.worldbank.org/indicator/AG.YLD.CREL.KG).
- **Període del fitxer:** 1961–2016. Es mostra el 2016, l'últim any disponible en aquest fitxer.
- El cereal inclou blat, arròs, blat de moro, ordi, civada, sègol, mill i sorgo, de manera que el valor també depèn de quin cereal es cultiva en cada país.

## Filtrat i preparació de les dades

### Dot density (`export.csv`)
1. **Files duplicades.** Entre 2015 i 2023 cada fila del fitxer original apareixia repetida de manera exacta, i s'han eliminat. Sense aquest pas, les superfícies d'aquests anys quedarien gairebé duplicades.
2. **Vivers i arbre ornamental.** S'exclou el grup *Vivers*, perquè fa servir una altra unitat (plantes per hectàrea) i no es pot sumar amb la resta.
3. **Superfície.** La columna `ha` és la suma de les quatre superfícies del fitxer original: secà i regadiu, en producció i no productiu.
4. **Municipis i comarques.** El fitxer original no té codi de municipi i algunes files tenien la comarca errònia (per exemple, el Prat de Llobregat apareixia a la Terra Alta). S'ha creuat cada municipi amb els límits oficials pel nom normalitzat i s'ha pres la comarca oficial. Set municipis han canviat de nom i s'han assignat a mà: Bigues i Riells (Bigues i Riells del Fai), Cabrera d'Igualada (Cabrera d'Anoia), Calonge (Calonge i Sant Antoni), Castell-Platja d'Aro (Castell d'Aro, Platja d'Aro i s'Agaró), Sant Carles de la Ràpita (La Ràpita), Saus (Saus, Camallera i Llampaies) i Vimbodí (Vimbodí i Poblet).
5. **Categories de conreu.** Olivera, Vinya, Ametller, Avellaner, Cítrics, Altres fruiters (la resta del grup de fruiters) i Altres llenyosos.
6. **Punts.** El 2024 hi ha 198.790 ha en 761 municipis. Cada municipi rep un punt per cada 50 ha de cada categoria; la fracció sobrant s'arrodoneix amb un sorteig, de manera que els municipis petits no desapareixen. Els punts es reparteixen a l'atzar dins del polígon del municipi (llavor fixa, 42, perquè el resultat sigui reproduïble).

Columnes de `export.csv`: `codi_municipi_6`, `nom_municipi`, `comarca_corregida`, `year`, `grup`, `cultiu`, `categoria`, `ha`.

### Small multiples (`fertilizer-use-per-hectare-of-cropland.csv`)
1. S'han mantingut només països: s'han descartat les regions FAO, agregats com la UE o el món, i països desapareguts (URSS, Iugoslàvia, Txecoslovàquia, entre d'altres). Queden 199 països.
2. S'han exclòs les Illes Fèroe, amb valors extrems de fins a 17.000 kg/ha.
3. Els nou països dels quadres són Espanya, Països Baixos, Estats Units, Brasil, Xina, Índia, Egipte, Ucraïna i Nigèria. S'han triat per contrast i no són una mostra representativa. El fitxer de `data/` conté només aquests nou.
4. Ucraïna comença el 1992 perquè no hi ha dades anteriors.
5. Tots els quadres comparteixen l'eix vertical (0–420 kg/ha) perquè es puguin comparar de manera directa.

Columnes: `pais`, `codi`, `year`, `kg_ha`.

### Beeswarm (`data.csv`)
1. S'han conservat només els països, amb el mateix criteri de codis ISO que el fitxer de fertilitzant, i s'han descartat els agregats del Banc Mundial (grups de renda, regions, OCDE, etc.).
2. S'ha afegit la columna `region` (continent) a partir del codi de país.
3. La columna `valor_extremo` marca els valors per sobre de 10.000 kg/ha. El 2016 són tres països: Kuwait, Sant Vicent i les Grenadines i els Emirats Àrabs Units. **No apareixen al gràfic**, perquè distorsionarien l'eix, però continuen al fitxer.
4. El gràfic mostra 174 països. Els punts es reparteixen en vertical només perquè no se solapin; l'alçada no vol dir res. La línia discontínua és la mediana de cada continent.

Columnes: `pais`, `codi`, `year`, `kg_ha`, `region`, `valor_extremo`.

## Limitacions
- La posició exacta de cada punt dins d'un municipi és aleatòria; el que és real és la quantitat de punts de cada municipi.
- El mapa mostra superfície de conreu llenyós, no producció, i no inclou vivers.
- El beeswarm usa el 2016 i el fertilitzant arriba al 2023, per això no es poden creuar directament.
- Les dades del Banc Mundial i de la FAO són estimacions oficials, i cada país les reporta amb criteris propis.
- El rendiment de cereal barreja cultius diferents, de manera que una xifra alta o baixa depèn també de quin cereal es cultiva.
- La selecció de països dels small multiples és per contrast i no representa el conjunt mundial.

## Tecnologia
- HTML, CSS i JavaScript sense cap framework, amb [D3](https://d3js.org/) v7.8.5 carregat des de cdnjs. Cal connexió a internet per carregar D3.
- La preparació de les dades s'ha fet amb Python (pandas, geopandas, shapely, pycountry).

## Veure-ho en local
Obre `index.html` al navegador. Les dades van incrustades a cada pàgina i només cal connexió per carregar D3. Si prefereixes servir-ho en local:

```bash
python3 -m http.server 8000
```

i obre `http://localhost:8000`.

## Publicació amb GitHub Pages
1. Puja el contingut d'aquest repositori a la branca `main`, amb `index.html` a l'arrel.
2. A *Settings → Pages*, tria *Deploy from a branch*, la branca `main` i la carpeta `/ (root)`.
3. Al cap d'un parell de minuts, les pàgines estaran a `https://<usuari>.github.io/<repositori>/`.

## Autoria
Pau Braun Arañó · Universitat Oberta de Catalunya · Visualització de dades, PAC 2.
