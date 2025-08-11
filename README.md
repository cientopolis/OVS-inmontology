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
io:listing_site2_A1405300735 a pronto:RealEstateListing ;
rdfs:label "Local Mar Del Plata Vendo O Permuto Por Auto U$s 19.000"^^xsd:string ;
dc:date "2024-01-29T00:00:00"^^xsd:dateTime ;
gr:hasBusinessFunction gr:Sell ;
sioc:about io:real_estate_site2_A1405300735 ;
sioc:has_creator io:account_site2_97219375 ;
sioc:has_space pronto:site2 ;
sioc:id "A1405300735"^^xsd:string ;
sioc:read_at "2024-02-02T00:00:00"^^xsd:dateTime ;
io:hasFeature [ a io:Precio ;
    io:hasDetail [ a io:TemporalFeature ;
    io:hasScraperTime [ a time:Instant ;
        time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] ;
    io:hasScraperValue [ a gr:UnitPriceSpecification ;
        gr:hasCurrency "USD"^^xsd:string ;
        gr:hasCurrencyValue "17500.0"^^xsd:float ;
        gr:priceType "BASE"^^xsd:string ] ] ] .

io:real_estate_site2_A1405300735 a io:Local ;
io:hasFeature io:feature_address_real_estate_site2_A1405300735 ;
rec:includes io:space_building_site2_A1405300735,
io:space_land_site2_A1405300735 .

io:feature_address_real_estate_site2_A1405300735 a io:Direccion ;
io:hasDetail [ a io:TemporalFeature ;
    io:hasScraperTime [ a time:Instant ;
        time:inXSDDateTimeStamp "2024-02-02T00:00:00"^^xsd:dateTime ] ;
    io:hasScraperValue [ a io:PostalAddress ;
        io:address "luro e independencia 3212"^^xsd:string ;
        io:city io:district_Mar%20del%20Plata_Bs.As.%20Costa%20Atlántica ;
        io:neighborhood io:neiborhood_province_Bs.As.%2520Costa%2520Atlática_district_Mar%2520del%2520Plata_Bs.As.%2520Costa%2520Atlántica_Centro ;
    io:province io:province_Bs.As.%20Costa%20Atlántica ] ] .
```