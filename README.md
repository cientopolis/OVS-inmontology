# Inmontology
Inmontology es una ontología que permite describir elementos del dominio inmobiliario, sus atributos y sus relaciones. El objetivo es proporcionar una estructura para datos provenientes de avisos inmobiliarios, considerando los inmuebles y sus características intrínsecas, los avisos inmobiliarios que publican esos inmuebles, y los agentes involucrados en la publicación de la oferta. En particular, la ontología permite almacenar los diversos valores que las características de un aviso o inmueble toman a lo largo del tiempo, así como también permite conocer su origen. Esto tiene sentido en el contexto que ciertos datos se pueden recuperar inicialmente a partir de los avisos inmobiliarios pero otros datos están embebidos en los textos del aviso, y se pueden extraer utilizando técnicas de extracción de información (AVE – Attribute-Value Extractor). En algunos casos puede haber ambigüedad en los valores de una misma característica, por lo que una persona puede realizar un curado manual para determinar el valor real de esa característica.
La siguiente imagen representa un resumen de algunas de las características más representativas de la ontología.

![](inmontology.png "Inmontology")



Notar que existen otras subclases de RealEstate y Feature, y se mencionan algunas a modo de ejemplo.
Si bien la ontología no restringe el uso del rango de valores de cada característica, sería deseable aplicar un SHACL para asegurarse que sean usadas de la manera esperada. Esto es, para la característica dirección (AddressFeature), los valores asociados deberían ser instancias de PostalAddress, y no por ejemplo de PriceSpecification.
Algunas características son propias del aviso (el Precio por ejemplo). Otras son del inmueble (como la Dirección), y otras describen alguna parte en particular de un inmueble (por ejemplo, describen el Terreno las Dimensiones de un inmueble, saber si es Irregular o no, si está en una esquina)

Cada característica tiene un valor, un origen y una marca temporal.
Los valores de las distintas características se modelan de manera diferente (por ejemplo el valor del precio se modela con una clase para representar un numero y una moneda). io:hasValue permite vincular los valores de una característica con la característica.
El origen indica de qué fuente se obtuvo el valor de ese dato, y se representa con las subclases de io:Origin.
Una marca temporal es un timestamp asociado al valor de la característica obtenida a partir de una fuente determinada, y se define a partir de la relación time:hasTime de una característica.

Por ejemplo, a continuación se ejemplifica un anuncio de un inmueble en venta, cuyo precio obtenido a partir de los datos del anuncio es 17000 USD. El inmueble en cuestión es un Local comercial, ubicado en la dirección Luro e Independencia 3212, Mar del Plata

```
:listing_site3_46639008 a pronto:RealEstateListing ;
    dc:date "2024-02-10T00:00:00"^^xsd:dateTime ;
    rdfs:label "Local en Venta Calle 56 Esquina 19"^^xsd:string ;
    gr:hasBusinessFunction gr:Sell ;
    sioc:about :real_estate_site3_46639008 ;
    sioc:has_creator :account_site3_Stephanie_Flores ;
    sioc:has_space pronto:site3 ;
    sioc:id "46639008"^^xsd:string ;
    sioc:read_at "2024-02-02T00:00:00"^^xsd:dateTime ;
    :hasListingFeature :feature_price_1_listing_site3_46639008 , :feature_price_2_listing_site3_46639008 , :feature_price_3_listing_site3_46639008.

:feature_price_1_listing_site3_46639008 a :BasePrice ;
    :hasPriceValue [ a gr:UnitPriceSpecification ;
            gr:hasCurrency "USD"^^xsd:string ;
            gr:hasCurrencyValue "50000.0"^^xsd:double ] ;
    :hasOrigin :Scraper ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] .

:feature_price_2_listing_site3_46639008 a :BasePrice ;
    :hasPriceValue [ a gr:UnitPriceSpecification ;
            gr:hasCurrency "USD"^^xsd:string ;
            gr:hasCurrencyValue "45000.0"^^xsd:double ] ;
    :hasOrigin :Scraper ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-03-02T00:00:00"^^xsd:dateTime ] .

:feature_price_3_listing_site3_46639008 a :BasePrice ;
    :hasPriceValue [ a gr:UnitPriceSpecification ;
            gr:hasCurrency "USD"^^xsd:string ;
            gr:hasCurrencyValue "45000.0"^^xsd:double ] ;
    :hasOrigin :Scraper ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-05-02T00:00:00"^^xsd:dateTime ] .

:real_estate_site3_46639008 a :Store ;
    :hasRealEstateFeature :feature_address_1_real_estate_site3_46639008,
        :feature_age_1_real_estate_site3_46639008 ,
        :feature_covered_1_space_land_site3_46639008,
        :feature_total_1_space_land_site3_46639008 ;
    rec:includes :space_building_site3_46639008,
        :space_land_site3_46639008 .

:space_building_site3_46639008 a rec:Building .

:space_land_site3_46639008 a rec:Site ;
    geo:hasGeometry [ a geo:Geometry ;
            geo:asWKT "<http://www.opengis.net/def/crs/EPSG/0/4326> POINT(-57.9577123 -34.9290869)"^^geo:wktLiteral ] .

:feature_address_1_real_estate_site3_46639008 a :Address ;
    :hasAddressValue [ a :PostalAddress ;
            :address "56 1100"^^xsd:string ;
            :city :municipality_La_Plata_province_Buenos_Aires ;
            :province :province_Buenos_Aires ] ;
    :hasOrigin :Scraper ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] .

:feature_covered_1_space_land_site3_46639008 a :CoveredSurface ;
    :hasSurfaceValue [ a pronto:SizeSpecification ;
            gr:hasUnitOfMeasurement "m²"^^xsd:string ;
            gr:hasValue "28"^^xsd:double  ] ;
    :hasOrigin :AVE ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] .

:feature_total_1_space_land_site3_46639008 a :TotalSurface ;
    :hasSurfaceValue [ a pronto:SizeSpecification ;
            gr:hasUnitOfMeasurement "m²"^^xsd:string ;
            gr:hasValue "28"^^xsd:double  ] ;
    :hasOrigin :AVE ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] .

:feature_age_1_real_estate_site3_46639008 a :PropertyAge ;
    :hasValue "10"^^xsd:integer ;
    :hasOrigin :Scraper ;
    :hasTime [ a time:Instant ;
            time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] .

:account_site3_Stephanie_Flores a sioc:UserAccount ;
    sioc:id "30357009"^^xsd:string ;
    sioc:name "Stephanie Flores"^^xsd:string .
```
