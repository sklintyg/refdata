# Gemensam referendata (masterdata)

### Inledning

Gemensam referensdata delas mellan applikationer och består av:

* Intygstexter
* Diagnoskoder och grupper
* Befattningskoder
* Postnummer
* Avtalstext

Tidigare har dessa legat under respektive applikation och även andra platser för stage och produktionsmiljöer.

Detta repository frikopplar releasecykeln för referensdata från respektive applikation och därmed kan en master-uppsättning av referensdata underhållas och livscykelhanteras.
     
### Applikationer

Nedan visas vilket data som används i respektive applikation.

| App | Gemensam referensdata |
| --- | --------------- | -------- |
| WC  | texts, links, postnummer, icf, diagnoskoder, befattnigskoder, privatlakaravtal |
| ST | links |
| MI | texts, links, befattnigskoder |
| RS | links, diagnoskoder, diagnoskapitel, diagnosgrupper |
| PP | links, postnummer |
| IT | texts, befattnigskoder |
| LS | |

_Not 1: Tidigare låg referensdata under `src/main/resources` för varje applikation och där återfanns för vissa applikationer även en `security` folder med `features.yaml` och `authorities.yaml` dessa båda filer var applikationsspecifik konfiguration och har därför flyttats med `default` representationer till `web/src/main/resources/security`._

_Not 2: Länkarna är fortfarande specifika per applikation men målsättningen är att även harmonisera dessa till en gemensam lista._

### Release

En förenklad releasehantering rekommenderas med en `master` branch som erhåller ett nytt byggnummer för varje uppdatering.


### Deployment

Referensdata byggs och levereras som en JAR-artefakt och applikationerna konfigureras med variabeln `REF_DATA_URL` som läser in angiven JAR från URLen ifråga. För test- och demomiljöer används den vanliga Nexus servern där de olika versionerna återfinns.




