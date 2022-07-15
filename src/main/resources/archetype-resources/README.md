${camelApplicationName}
===

Questa applicazione è stata generata con
[Spring Enterprise JPA Application with Client UI Archetype](http://gitlab.carige.web/development-general/java/maven-archetypes/spring-enterprise-jpa-application-with-client-ui-archetype.git).

Struttura
---

${camelApplicationName} è un'applicazione Spring Boot multi-modulo, articolata sui seguenti moduli:

- `client` - contiene l'applicazione client Angular, esposta alla path `/${rootArtifactId}`.
  Benché incorporata in
  questo medesimo progetto Maven, **l'applicazione client Angular è da considerarsi una componente
  del tutto disaccoppiata rispetto alla rispettiva componente server Java**. In particolare si
  suggerisce di sviluppare il codice dell'applicazione Angular in maniera indipendente dal progetto
  Maven in cui è inserita, adottando come workspace la directory `${rootArtifactId}-client/src/angular`,
  ignorando tutto ciò che sta al di fuori di essa, servendo l'applicazione con il consueto `ng serve`,
  e "mockando" i servizi della componente server con un tool apposito e.g. Mockoon. ***L'applicazione
  client deve rigorosamente limitare le proprie responsabilità alla sola logica di presentazione,
  qualsiasi altra funzione deve essere delegata alla componente server.*** Ne consegue in particolare
  che l'applicazione client Angular deve effettuare chiamate *solo* verso la propria componente server,
  la quale si farà carico eventualmente di implementare la logica di integrazione con le componenti server
  di altre applicazioni.

- `server` - contiene la componente server del progetto, dove si accentra tutta la logica che non sia
  di presentazione. La componente server è l'unico interlocutore dell'applicazione client Angular.

  - `data` - contiene le interfacce DAO di Spring Data JPA; gli sviluppatori sono incoraggiati a
    parametrizzare il più possibile il comportamento dell'applicazione client tramite apposite entity
    su un database dedicato, in maniera tale da poter modificare dinamicamente la cofigurazione
    dell'applicazione client.

  - `factory` - contiene le classi factory, responsabili della costruzione dei DTO; in termini
    generali, ogni DTO contenuto in `${package}.model.dto`, ad eccezione dei DTO di tipo
    `request` e `response`, deve essere costruito da un'apposita classe factory

  - `feature` - destinato a contenere la logica di business dell'applicazione; ogni elemento
    funzionale *deve* risiedere in un package dedicato, come accade per le due feature
    esemplificative contenute nell'archetipo; ogni feature *deve* rispettare la suddivisione
    `controller`/`service`, come accade per le due feature esemplificative contenute nell'archetipo

  - `framework` - contiene il codice trasversale alla logica di business (modalità di logging,
    eccezioni, messaggi pre-impostati); l'idea è quella di gestire queste tematiche in modo
    omogeneo tra le diverse applicazioni Dock (e.g. in maniera tale che ad una stessa casistica
    di errore possa corrispondere la stessa eccezione in tutte le applicazioni; o in maniera
    tale che tutte le applicazioni scrivano log allo stesso modo)

  - `integration` - destinato a contenere la logica di iintegrazione con altre componenti
    applicative, ad esempio client SOAP; in fase di creazione è vuoto

  - `model` - contiene i DTO ed eventualmente le entity gestite dall'applicazione; il modulo
    `model` viene esportato su Artifactory ad ogni build, in maniera tale che le applicazioni
    possano "condividere" i data model se necessario (e.g. se un'applicazione `A` invoca un
    servizio di un'applicazione `B` non deve scrivere degli appositi DTO per "scimmiottare"
    le response dell'applicazione `B`, ma può semplicemente importarne il modulo `model`)

  - `war` - è il punto di partenza dell'applicazione, contiene la classe main e la
    relativa configurazione; in condizioni normali questo modulo non viene modificato
    rispetto a quanto generato in fase di creazione, perché la logica rilevante risiede
    tipicamente nei moduli "funzionali"; in fase di build, l'output di questo modulo è
    un archivio `.war`

  - `ear` - modulo "di servizio", responsabile del packaging dell'applicazione in formato `.ear`

Autenticazione
---
Di default, la path `/${rootArtifactId}` è protetta da autenticazione i.e. cercando di visualizzare
l'applicazione client dal browser direttamente alla path `/${rootArtifactId}` si riceverà un errore
`401 - Unauthorized`. È invece possibile accedere all'applicazione tramite il processo previsto dal
NIC, ovvero invocando la path `/${rootArtifactId}/authentication` con metodo `POST`, fornendo nel
payload una mappa contenente le informazioni dell'utente. Se il parsing delle informazioni va a buon
fine, l'applicazione effettuerà automaticamente il redirect alla path `/${rootArtifactId}`, ma
stavolta con un'autenticazione valida e quindi con la opssibilità di visualizzare correttamente i
contenuti dell'applicazione Angular.


Unit test
---

Appena creata, la componente server di ${camelApplicationName} ha un test coverage del 100%, ottenuto tramite

- la scrittura di unit test per le feature esemplificative che vengono generate insieme
  allo scheletro dell'applicazione (e.g. `${package}.feature.administration`)

- la definizione di pattern di esclusione dal calcolo del coverage (property `sonar.coverage.exclusions`
  nel parent-pom dell'applicazione).

`TODO:` provvedere alla medesima configurazione relativamente alla componente client.

Code analysis
---

Appena creata, ${camelApplicationName} è pronta per essere sottoposta a scansione statica del codice sorgente,
tramite SonarQube. Le property di dettaglio riferite all'integrazione con SonarQube sono contenute nel
parent-pom dell'applicazione.

Come già scritto, le componenti server e client sono da intendersi come completamente disaccoppiate, quindi
occorre effettuare scansioni SonarQube *distinte* per l'una e per l'altra.