# Gemensam referendata (masterdata)

## Inledning

Gemensam referensdata delas mellan applikationer och består av:

* Intygstexter
* Diagnoskoder och grupper
* Befattningskoder
* Postnummer
* Avtalstext

Tidigare har dessa legat under respektive applikation och även andra platser för stage och produktionsmiljöer.

Detta repository frikopplar releasecykeln för referensdata från respektive applikation och därmed kan en master-uppsättning av referensdata underhållas och livscykelhanteras.
     
## Applikationer

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


## Releasehantering

En förenklad releasehantering rekommenderas med en `master` branch som erhåller ett nytt byggnummer för varje uppdatering. Vid en eventuell större strukturförändring skapas en särskild release branch för legacy. Detta innebär vidare att applikationerna måste konfigurera en `REFDATA_URL` som pekar på den version som ska användas, se mer nedan.


## Användning

Referensdata byggs och levereras som en JAR-artefakt och applikationerna konfigureras med variabeln `REFDATA_URL`.
Majoriteten av refdata ändras ganska sällan och bör vara samma oavsett miljö (koder, postnummer, länkar etc). Dock skiljer sig texterna en smula då det finns features som kräver att testdata skiljer sig mellan miljöerna. Exempelvis finns hantering av tilläggsfrågor och även datum för aktivering av intyg som båda måste testas av i utveckling och olika testmiljöer. 

#### Lokal utvecklingsmiljö och bygg-pipelines (build-inera.nordicmedtest.se)

Rekommendationen är att de 3 applikationer (WC, MI och ST) som hanterar texter använder liknande koncept som infra och common där man bygger en `0-SNAPSHOT` version av refdata som används vid lokal testning. Snapshot releaser kommer dessutom att laddas upp till Nexus och finns ingen lokalt installerad `0-SNAPSHOT` så kommer denna till skillnad från common och infra att laddas ned från Nexus.

I OpenShift Jenkins laddas senaste `0-SNAPSHOT` automatiskt ned från Nexus från [Nexus](https://nexus.drift.inera.se/#browse/browse/components:snapshots) förutsatt att ingen annan konfiguration har angivits med `REFDATA_URL`.

#### Test, stage och produktion

Vill man skapa en variant av `master` så görs detta genom att plocka ut en branch som man modifierar för att manuellt trigga ett release bygge på [klassisk Jenkins](https://build-inera.nordicmedtest.se/jenkins/view/Release/job/release-refdata/build) där man anger branch och även kan namnge bygget. Sedan pekas artefakten ut med `REFDATA_URL` i applikationens konfiguration.  


För övriga miljöer pekas refdata JAR-artefakten ut med `REFDATA_URL`, denna pekar företrädesvis på en Nexus server men kan även peka mot annan tjänst eller direkt mot ett filsystem.

Handlar det om ett filsystem så måste en fullt kvalificerad URL användas, exempel på en inställning i OpenShift ConfigMap:
	
	REFDATA_URL: "file://localhost/path/refdata-0-SNAPSHOT.jar"
