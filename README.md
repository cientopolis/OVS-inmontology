# inmontology

**inmontology** es una ontología OWL para describir publicaciones inmobiliarias, propiedades, sus características y datos territoriales y ferroviarios. El vocabulario está pensado para integrar información de conjuntos de datos inmobiliarios y vincularla con ontologías existentes.

## Contenido del repositorio

- [`inmontology.ttl`](inmontology.ttl): ontología principal en Turtle, formato recomendado para leer y editar.
- [`inmontology.rdf`](inmontology.rdf): serialización RDF/XML de la ontología principal.
- [`spatial.rdf`](spatial.rdf): módulo territorial y ferroviario en RDF/XML.
- `rec/`, `pronto/`, `geosparql/`, `gr/`, `sioc/`, `time/`, `foaf/` y `dc/`: copias locales de vocabularios externos referenciados por la ontología.

La ontología principal tiene el IRI `http://sw.cientopolis.org/inmontology/`. Sus términos centrales usan el espacio de nombres `http://sw.cientopolis.org/inmontology#` (prefijo `io:`). El módulo espacial se identifica con `http://sw.cientopolis.org/inmontology/spatial`; sus términos territoriales usan `/ter#` y los ferroviarios `/rail#`.

## Modelo conceptual

### Publicaciones, propiedades y características

Las publicaciones inmobiliarias se representan con `pronto:RealEstateListing`, que extiende `gr:Offering` y `sioc:Post`. Las propiedades se modelan con `rec:RealEstate`; inmontology agrega tipos más específicos, como `io:Apartment`, `io:House`, `io:Land`, `io:Condominium`, `io:Farm`, `io:Store`, `io:Hotel`, `io:ParkingLot` y `io:Warehouse`.

`io:Feature` es la clase base para las características y permite asociar un origen (`io:hasOrigin`), un valor (`io:hasValue`) y una marca temporal (`io:hasTime`). Se distinguen:

- `io:RealEstateFeature`, para atributos de la propiedad, como superficies, antigüedad, orientación de fachada, piscina o dimensiones del lote.
- `io:ListingFeature`, para atributos de la publicación, como precio, preventa o publicación de varias propiedades.

Las propiedades `io:hasRealEstateFeature` y `io:hasListingFeature` vinculan cada clase de características con el recurso correspondiente. `io:hasLiteralValue` sirve para valores literales; `io:hasSurfaceValue` y `io:hasPriceValue` enlazan especificaciones de tamaño y precio, respectivamente. El vocabulario también incluye `io:curatedBy` e `io:reviewedBy` para registrar cuentas responsables de curar o revisar características.

Los orígenes (`io:Origin`) incluyen `io:AVE` para valores obtenidos mediante extracción automática de atributos y `io:Scraper` para datos recuperados de sitios web. `io:Curation` permite identificar el enriquecimiento o la revisión manual. La marca temporal puede representar cuándo se obtuvo o registró un valor. Esto permite conservar procedencia y cambios de una característica a lo largo del tiempo.

### Direcciones y territorio

El vocabulario principal define `io:Address`, `io:PostalAddress` y las relaciones `io:hasAddressValue`, `io:province`, `io:municipality`, `io:town` e `io:neighborhood`. El módulo espacial extiende `rec:Region` y GeoSPARQL con regiones canónicas (`ter:Region`), provincias, departamentos, localidades y barrios. `ter:locatedIn` es transitiva y subpropiedad de `rec:locatedIn`, para expresar jerarquías territoriales.

### Ferrocarriles

El módulo espacial modela `rail:RailwayFeature` como una entidad geográfica de GeoSPARQL, con `rail:RailwayLine` y `rail:Station` como subclases.

## Ejemplo Turtle

El siguiente fragmento muestra aviso real del conjunto de datos enlazado con un local comercial, sus características y dos valores de precio registrados en fechas diferentes. 

```turtle
@prefix io: <http://sw.cientopolis.org/inmontology#> .
@prefix gr: <http://purl.org/goodrelations/v1#> .
@prefix rec: <https://w3id.org/rec/core/> .
@prefix time: <http://www.w3.org/2006/time#> .
@prefix pronto: <http://sw.cientopolis.org/pronto#> .
@prefix dcterms: <http://purl.org/dc/terms/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix sioc: <http://rdfs.org/sioc/ns#> .

io:listing_site3_46639008 a pronto:RealEstateListing ;
    dcterms:issued "2024-02-10T00:00:00-03:00"^^xsd:dateTimeStamp ;
    rdfs:label "Local en Venta Calle 56 Esquina 19"^^xsd:string ;
    gr:hasBusinessFunction gr:Sell ;
    sioc:about io:real_estate_site3_46639008 ;
    io:hasListingFeature io:feature_price_1_listing_site3_46639008,
        io:feature_price_2_listing_site3_46639008 .

io:real_estate_site3_46639008 a io:Store ;
    io:hasRealEstateFeature io:feature_address_1_real_estate_site3_46639008,
        io:feature_age_1_real_estate_site3_46639008,
        io:feature_covered_1_real_estate_site3_46639008,
        io:feature_total_1_real_estate_site3_46639008 ;
    rec:includes io:space_building_site3_46639008,
        io:space_land_site3_46639008 .

io:feature_address_1_real_estate_site3_46639008 a io:Address ;
    io:hasAddressValue [ a io:PostalAddress ;
        io:address "56 1100"^^xsd:string ;
        io:municipality io:municipality_La_Plata_province_Buenos_Aires ;
        io:province io:province_Buenos_Aires ] .

io:feature_age_1_real_estate_site3_46639008 a io:PropertyAge ;
    io:hasLiteralValue "10"^^xsd:integer ;
    io:hasTime [ a time:Instant ;
        time:inXSDDateTimeStamp "2024-02-02T00:00:00-03:00"^^xsd:dateTimeStamp ] .

io:feature_covered_1_real_estate_site3_46639008 a io:CoveredSurface ;
    io:hasSurfaceValue [ a pronto:SizeSpecification ;
        gr:hasUnitOfMeasurement "m²"^^xsd:string ;
        gr:hasValue "28"^^xsd:float ] .

io:feature_price_1_listing_site3_46639008 a io:BasePrice ;
    io:hasPriceValue [ a gr:UnitPriceSpecification ;
        gr:hasCurrency "USD"^^xsd:string ;
        gr:hasCurrencyValue "50000.0"^^xsd:float ] ;
    io:hasTime [ a time:Instant ;
        time:inXSDDateTimeStamp "2024-02-02T00:00:00-03:00"^^xsd:dateTimeStamp ] .

io:feature_price_2_listing_site3_46639008 a io:BasePrice ;
    io:hasPriceValue [ a gr:UnitPriceSpecification ;
        gr:hasCurrency "USD"^^xsd:string ;
        gr:hasCurrencyValue "45000.0"^^xsd:float ] ;
    io:hasTime [ a time:Instant ;
        time:inXSDDateTimeStamp "2024-03-02T00:00:00-03:00"^^xsd:dateTimeStamp ] .
```

El ejemplo ilustra cómo el aviso se relaciona con la propiedad mediante `sioc:about`; las características del aviso y del inmueble se enlazan por separado. También muestra valores tipados con GoodRelations/Pronto y la asociación temporal que permite conservar cambios, como la reducción del precio. En el grafo completo se incluyen más datos del aviso, la cuenta que lo publicó, superficies y geometría.

OWL define clases, relaciones y rangos para el razonamiento; para validar requisitos estrictos de los datos (por ejemplo, que toda dirección tenga una dirección postal asociada), puede complementarse con formas SHACL. Este repositorio no incluye actualmente un conjunto de formas SHACL.

## Vocabularios relacionados

La ontología reutiliza términos de:

- **REC** (`https://w3id.org/rec/`): conceptos de edificios, unidades, propiedades, espacios y regiones.
- **Pronto** (`http://sw.cientopolis.org/pronto#`): publicaciones inmobiliarias y especificaciones de tamaño.
- **GoodRelations** (`http://purl.org/goodrelations/v1#`): ofertas y especificaciones de precio.
- **GeoSPARQL** (`http://www.opengis.net/ont/geosparql#`): entidades y geometrías geográficas.
- **SIOC**, **FOAF** y **OWL-Time**: publicaciones, cuentas y datos temporales.

Los archivos de vocabularios externos incluidos en el repositorio son copias locales de referencia; los identificadores RDF siguen siendo los IRI publicados por cada vocabulario.

## Uso

Los archivos pueden cargarse en herramientas compatibles con RDF/OWL. Use `inmontology.ttl` para inspección y edición legible, o `inmontology.rdf` si su herramienta requiere RDF/XML. Para trabajar con el módulo territorial y ferroviario, cargue también `spatial.rdf` (o el IRI de la ontología espacial cuando su entorno resuelva importaciones).

El repositorio contiene vocabularios, no una aplicación ejecutable: no define comandos de instalación, compilación o ejecución.


