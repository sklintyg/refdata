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
| --- | --------------------- |
| WC  | texts, links, postnummer, icf, diagnoskoder, befattnigskoder, privatlakaravtal |
| ST | links,  diagnoskoder |
| MI | texts, links, befattnigskoder |
| RS | links, diagnoskoder, diagnoskapitel, diagnosgrupper |
| PP | links, postnummer |
| IT | texts, befattnigskoder |
| LS | |

_Not 1: Tidigare låg referensdata under `src/main/resources` för varje applikation och där återfanns för vissa applikationer även en `security` folder med `features.yaml` och `authorities.yaml` dessa båda filer var applikationsspecifik konfiguration och har därför flyttats med `default` representationer till `web/src/main/resources/security`._

_Not 2: Länkarna (links) är fortfarande specifika per applikation men målsättningen är att även harmonisera dessa till en gemensam lista._

### Deployment

Referensdata byggs och levereras som en JAR-artefakt och applikationerna konfigureras med variabeln `REFDATA_URL` som läser in angiven JAR från URLen ifråga. Är inte någon URL angiven så hämtas den senaste versionen från NMT Nexus server [https://build-inera.nordicmedtest.se](https://build-inera.nordicmedtest.se). 

### Releasehantering

En förenklad releasehantering rekommenderas med en `master` branch som erhåller ett nytt byggnummer för varje uppdatering. Vid en eventuell större strukturförändring skapas en särskild release branch för legacy. Detta innebär vidare att legacy applikationerna måste konfigurera en `REFDATA_URL`, se mer nedan.

### Utvecklings och testmiljöer

Majoriteten av refdata ändras ganska sällan och bör vara samma oavsett miljö (koder, postnummer, länkar etc). Dock skiljer sig texterna en smula då det finns features som kräver att testdata skiljer sig mellan miljöerna. Exempelvis finns hantering av tilläggsfrågor och även datum för aktivering av intyg som båda måste testas av i utveckling och olika testmiljöer. 

Rekommendationen är därför att de 3 applikationer (WC, MI och ST) som hanterar texter använder samma koncept under utveckling som infra och common där man bygger en '0-SNAPSHOT' version av refdata som används vid lokal testning. För test-pipeline och andra testmiljöer används en branch som pekas ut med miljövariablerna `REFDATA_URL` och `RESOURCES_FOLDER`.

Exempel, använd branch `devtest` för OpenShift pipeline:

	# Inställningar i applikationens ConfigMap
	REFDATA_URL: "https://github.com/sklintyg/refdata/archive/devtest.zip"
	RESOURCES_FOLDER: "classpath:refdata-devtest/src/main/resources"

_Observera att det är en GitHub specifik feature som används för att ladda ned ett en viss branch av ett repo som ett ZIP-arkiv._

Alternativt kan JAR-filen med refdata konfigureras med `REFDATA_URL` till att peka på antingen en HTTP tjänst eller ett delat filsystem. Handlar det om ett filsystem så måste en fullt kvalificerad URL användas, exempel:
	
	REFDATA_URL: "file://localhost/path/refdata-0-SNAPSHOT.jar"
	RESOURCES_FOLDER: "classpath:"

	 