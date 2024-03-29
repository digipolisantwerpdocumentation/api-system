## Resource representatie

### Taal

#### API's in het Engels <span class="rule-ref">R-TL-001</span> :id=R-TL-001

Om onze API's zo breed mogelijk bruikbaar te maken worden ze in het **Engels** gemaakt.

### Formaat
#### Formaat API documentatie is Swagger 2.0 of OpenAPI Specificatie 3.0 <span class="rule-ref">R-FM-001</span> :id=R-FM-001


### JSON conventies

Hanteer onderstaande conventies voor berichten die JSON als payload formaat hebben.

#### Gebruik steeds dubbele quotes bij keys <span class="rule-ref">R-JS-001</span> :id=R-JS-001
``` json
"company" : "Digipolis"
```

#### Gebruik steeds dubbele quotes bij string values <span class="rule-ref">R-JS-002</span> :id=R-JS-002
``` json
"company" : "Digipolis"
```

#### Gebruik steeds camelCase om keys weer te geven <span class="rule-ref">R-JS-003</span> :id=R-JS-003
``` json
"addressLine" : "Generaal Armstrongweg"
```

#### Gebruik geen dots "." in keys <span class="rule-ref">R-JS-004</span> :id=R-JS-004
``` json
"address.street" : "Generaal Armstrongweg" wordt NIET toegelaten (gebruik dan hiërarchieën)
```

#### Keys mogen niet starten met cijfers <span class="rule-ref">R-JS-005</span> :id=R-JS-005
Dit verlaagt immers de leesbaarheid.
``` json
"5street" : "Generaal Armstrongweg" wordt NIET toegelaten
```

#### Verwijder `null` waardes uit de resource representatie indien deze geen betekenis hebben <span class="rule-ref">R-JS-006</span> :id=R-JS-006
``` json
"middleName" : null
```

#### Toon lege waardes in de resource representatie <span class="rule-ref">R-JS-007</span> :id=R-JS-007
``` json
"middleName" : "",
"orders" : []
```

#### Encapsuleer arrays steeds in een object <span class="rule-ref">R-JS-008</span> :id=R-JS-008
Dit omdat bepaalde frameworks niet goed overweg kunnen met native arrays
``` json
{
   "adresses":[
      {
         "street":"Generaal Armstrongweg",
         "number":2,
         "zip":2000,
         "city":"Antwerpen"
      },
      {
         "street":"Britselei",
         "number":57,
         "zip":2000,
         "city":"Antwerpen"
      }
   ]
}
```

### Datums en timestamps
#### Formatteer datums en timestamps volgens RFC3339 <span class="rule-ref">R-DT-001</span> :id=R-DT-001
Formatteer datums en timestamps steeds volgens [RFC3339](https://tools.ietf.org/html/rfc3339). JSON definieert immers geen standaard formaat voor datums en timestamps.

> RFC3339 volgt de ISO 8601 standaard maar heeft enkele optimalisaties voor het internet en machine to machine communicatie. Bijgevolg is deze ideaal voor REST API's)

``` json
"finishedAt" : "2012-01-01T01:00:00+01:00"
```

Hier enkele basis regels voor het gebruik van datum en/of tijd in jouw API:

1. **Aanvaard eender welke tijdzone:** Gebruikers kunnen overal ter wereld zitten of zelfs in een buurland met een andere tijdzone. Je kan moeilijk als API bouwer gokken in welke tijdzone ze zitten

2. **Werk met UTC:** Dit wil zeggen:
    * Converteer de ontvangen tijdzone naar UTC en
    * Bewaar in jou data storage gegevens in UTC. Dit maakt het ook makkelijker om te queryen. (Stel dat je data met een lokale tijdzone bewaart en er wordt een query uitgevoerd met input vanuit een andere tijdzone, dan moet je zelf in uw query deze verschillen afhandelen.)
    * Maak gebruik van de tijdzone notatie "2012-01-01T00:00:00+01:00" (de tijd is gespecifieerd in de lokale tijd van de aanbieder). De Zulu notatie 2019-02-04T23:20:11Z mag anderzijds ook gebruikt worden.

3. **Geef UTC terug:** Zo kunnen afnemers zelf bepalen hoe zij de data weergeven. Vergeet niet in de API docs dit te vermelden.

4. **Gebruik enkel time indien nodig:** Je kan je snel vergissen met dagen als tijd mee genoteerd wordt. Stel dat 2019-02-04T23:59:59Z als deadline voor een project is genoteerd. Een vergissing met vertaling naar een andere tijdzone levert snel 5 februrari op, in plaats van 4 februari. Gebruik met andere woorden ```date``` als je een dag wil noteren en ```datetime``` als je een tijdstip wil noteren.


### Durations
#### Formatteer durations volgens ISO8601 <span class="rule-ref">R-DU-001</span> :id=R-DU-001
Durations worden geformatteerd volgens [ISO8601](https://en.wikipedia.org/wiki/ISO_8601).
``` prettyprint
"duration" : "P0003-04-06T12:00:00" (3 jaar, 4 maanden, 6 dagen, 12 uur)
```

### Geospatiale data
#### Formatteer geospatiale data volgens RFC7946 <span class="rule-ref">R-GS-001</span> :id=R-GS-001
Alle geospatiale data wordt steeds geformatteerd volgens [RFC7946](https://tools.ietf.org/html/rfc7946)

Deze standaard laat toe om van eenvoudige locatie objecten in een longitude, latitude array...

``` json
     {
         "type": "Point",
         "coordinates": [100.0, 0.0]
     }
```

tot meer complexe geospatiale objecten zoals bijvoorbeeld een polygoon (en nog veel meer) te definiëren.

``` json
     {
         "type": "Polygon",
         "coordinates": [
             [
                 [100.0, 0.0],
                 [101.0, 0.0],
                 [101.0, 1.0],
                 [100.0, 1.0],
                 [100.0, 0.0]
             ],
             [
                 [100.8, 0.8],
                 [100.8, 0.2],
                 [100.2, 0.2],
                 [100.2, 0.8],
                 [100.8, 0.8]
             ]
         ]
     }
```

### Hiërarchie
#### Structureer resource hiërarchisch ipv vlak <span class="rule-ref">R-HA-001</span> :id=R-HA-001
Kies steeds voor een hiërarchisch gestructureerde resource representatie in plaats van een vlakke structuur. Een hiërarchische voorstelling biedt als grote voordeel dat het overzichtelijker en duidelijker is voor resources met een groot aantal velden. Daarnaast biedt het als grote voordeel het hergebruik van entiteiten over APIs heen.

Als algemene richtlijn kan je stellen dat wanneer het aantal velden \> 15 je best naar een hiërarchische structuur kan overgaan indien dit meer duidelijkheid schept.

Een voorbeeld van een hiërarchische structuur
```json
{
  "company": "Google",
  "website": "http://www.google.com/",
  "address": {
    "line1": "111 8th Ave",
    "line2": "4th Floor",
    "state": "NY",
    "city": "New York",
    "zip": "10011"
   }
 }
```

met daar tegenover dezelfde representatie maar dan in een vlakke structuur
```json
{
  "company": "Google",
  "website": "http://www.google.com/",
  "addressLine1": "111 8th Ave",
  "addressLine2": "4th Floor",
  "state": "NY",
  "city": "New York",
  "zip": "10011"
}
```
