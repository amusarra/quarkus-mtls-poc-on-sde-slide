---
theme: default
background: /images/slide_background_all_pages.png
class: text-white
title: Autenticazione mTLS e Controllo degli Accessi con Quarkus
  SecurityIdentityAugmentor per prevenire SDE
author: Antonio Musarra
keywords: quarkus,mtls,security,securityidentity,augmentor,sensitive-data-exposure
info: |
  ## mTLS e Quarkus
  Presentazione di una PoC per dimostrare come l'autenticazione mTLS (Mutual TLS) implementata con Quarkus contribuisca in modo significativo alla prevenzione della Sensitive Data Exposure (SDE) garantendo che solo i client autorizzati possano accedere a risorse protette.

  Autore: Antonio Musarra

  Tag: v1.0.0
layout: cover
---

# mTLS con Quarkus

## Controllo degli accessi con Security Identity Augmentor

## per prevenire Sensitive Data Exposure (SDE A02:2021-Cryptographic Failures )

<!--
L'obiettivo di questa PoC è quello di dimostrare come l'autenticazione mTLS (Mutual TLS), implementata con Quarkus, contribuisca in modo significativo alla prevenzione della Sensitive Data Exposure (SDE) garantendo che solo i client autorizzati possano accedere a risorse protette.
-->

---

# mTLS – Tipo un'Autenticazione Doppia (Ricordiamocelo!)

- **TLS "normale":**
    - Fa viaggiare le tue cose in modo sicuro, criptato.
    - Tu controlli solo che il sito a cui ti connetti sia quello vero (hai presente il lucchetto nel browser?).
- **mTLS (Mutual TLS):**
    - È come il TLS, ma fa un passo in più!
    - Non solo tu controlli il sito, ma **ANCHE** il sito controlla te!
    - Usate certificati digitali per dirvi "Sono proprio io!".
    - Canale super sicuro dove sai *chi* c'è dall'altra parte.

<!--
Essenzialmente, mTLS è un modo per garantire che entrambe le parti in una comunicazione siano chi dicono di essere. Questo è particolarmente utile quando si tratta di proteggere dati sensibili e garantire che solo i client autorizzati possano accedere a risorse protette.
-->
---

# Il Progetto Quarkus mTLS – La Base Sicura di partenza

- **Progetto GitHub:** [amusarra/quarkus-mtls-auth](https://github.com/amusarra/quarkus-mtls-auth)
- **Quarkus TLS Registry
  ** [https://quarkus.io/guides/tls-registry-reference](https://quarkus.io/guides/tls-registry-reference)
- **Implementazione di TLS Mutual Authentication (mTLS) con Quarkus:** [https://bit.ly/3MQPA3v](https://bit.ly/3MQPA3v)
- **Attivazione lato server:**
    - In `application.properties`: `quarkus.http.ssl.client-auth=required`
    - Indicare percorsi dei certificati
- **Creazione Certificati:**
    - Usa gli script in `src/main/shell/certs-manager/`

<div v-click class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-70 z-50">
    <video src="/images/asciinema/poc_quarkus_mtls_clonazione_avvio_app.mp4" controls muted loop width="800"></video>
</div>

<div v-click class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-70 z-50">
  <video src="/images/asciinema/poc_quarkus_mtls_prima_verifica_configurazione_mtls_x3.mp4" controls muted loop width="800"></video>
</div>

<!--
Per questa PoC userò il progetto Quarkus-mTLS-auth che ho sviluppato qualche tempo a supporto dell'articolo "Implementazione di TLS Mutual Authentication (mTLS) con Quarkus" che ho pubblicato sul mio blog.

In questo progetto, ho implementato l'autenticazione mTLS in Quarkus, utilizzando il TLS Registry di Quarkus per gestire i certificati e le chiavi. Ho anche incluso uno script per generare i certificati necessari per testare l'autenticazione mTLS.

Per attivare l'autenticazione mTLS lato server, è sufficiente impostare la proprietà `quarkus.http.ssl.client-auth=required` nel file `application.properties` e specificare i percorsi dei certificati. Inoltre, ho incluso uno script in `src/main/shell/certs-manager/` per generare i certificati necessari a scopo di test.
-->
---

# La Sicurezza di Quarkus e il Security Identity Augmentor

- **Sicurezza flessibile di Quarkus**
- **Autenticazione:**
    - Quarkus crea una **"carta d'identità"** interna = `SecurityIdentity`
- **Augmentor = aiutante speciale**
    - Aggiunge info alla SecurityIdentity di base
    - Puoi leggere i dati *direttamente* dal certificato client

<div v-click class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-70 z-50">
  <img src="https://www.dontesta.it/wp-content/uploads/2024/09/quarkus-security-architecture-overview.png" class="max-h-[60vh] rounded-xl shadow-2xl border-4 border-white" />
</div>

<!--
Quarkus offre un sistema di sicurezza flessibile che consente di gestire l'autenticazione e l'autorizzazione in modo semplice ed efficace. Quando un client si autentica, Quarkus crea una "carta d'identità" interna chiamata `SecurityIdentity`, che contiene informazioni sull'identità del client.

Inoltre, Quarkus offre un "Augmentor", un componente che consente di arricchire la `SecurityIdentity` con informazioni aggiuntive. Questo è particolarmente utile quando si desidera estrarre dati specifici dai certificati client, come i ruoli o altre informazioni pertinenti.

In sintesi, la figura mostra come Quarkus gestisce la sicurezza: prima autentica chi sei (verificando le credenziali), poi arricchisce la tua identità con dettagli rilevanti (grazie all'Augmentor), e infine decide se puoi accedere alla risorsa richiesta (autorizzazione).

Ecco i passaggi chiave:

Client e Richiesta HTTP: Tutto inizia con un Client che invia una Richiesta HTTP.

Quarkus Security manager: Questo componente intercetta la richiesta e avvia il processo di autenticazione e autorizzazione.

HttpAuthenticationMechanism: Questo meccanismo estrae le credenziali di autenticazione dalla richiesta (ad esempio, un certificato mTLS, un header Basic Auth, un token JWT, ecc.). Se l'autenticazione fallisce, può creare una "authentication challenge" (es. chiedere le credenziali).

IdentityProvider: Utilizzando le credenziali estratte, l'Identity Provider verifica l'identità dell'utente o del servizio. Se la verifica fallisce, l'autenticazione fallisce. Se ha successo, crea una SecurityIdentity.

SecurityIdentityAugmentor: Questo è un punto cruciale. Il SecurityIdentityAugmentor personalizza/arricchisce la SecurityIdentity creata dall'Identity Provider, aggiungendo informazioni aggiuntive come Ruoli, Permessi o altri Attributi estratti dalle credenziali originali (come le estensioni di un certificato client mTLS).

Authorization: Basandosi sulla SecurityIdentity arricchita (che ora include ruoli/attributi), il componente di Autorizzazione verifica se l'identità ha il permesso di accedere alla risorsa richiesta, in base a regole definite (Ruoli, Permessi, HttpSecurity Policy).

Accesso: Se l'autorizzazione ha successo, l'Accesso è garantito e la richiesta raggiunge l'applicazione Quarkus. Se l'autorizzazione fallisce, l'Accesso viene negato.
-->
---

# Prendere Ruoli e Altre Info dai Certificati (Che Idea!)

- **Security Identity Augmentor:**
    - Estrae info da certificato (es. `Role`, `DeviceId`)
    - Aggiunge questi dati alla SecurityIdentity
- **Importanza:**
    - Trasforma **"Ok, sei entrato"** in **"Ok, sei entrato, sei ADMIN e usi dispositivo X"**
    - Serve per decidere cosa può fare quell'identità = autorizzazione

<div v-click class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-70 z-50">
  <img src="/images/client_certs_estensioni_custom_1.jpg" class="max-h-[60vh] rounded-xl shadow-2xl border-4 border-white" />
</div>

<!--
L'Augmentor di Quarkus consente di estrarre informazioni dai certificati client, come i ruoli o altri dati pertinenti, e di aggiungerli alla `SecurityIdentity`. Questo è fondamentale perché trasforma una semplice autenticazione in un'autenticazione arricchita, che fornisce informazioni più dettagliate sull'identità del client.

Sui certificati x509 è possibile definire estensioni personalizzate, come `Role` o `DeviceId`, che possono essere utilizzate per arricchire la `SecurityIdentity`. Ad esempio, un certificato potrebbe contenere un'estensione `Role` con il valore `ADMIN`, che indica che l'utente ha privilegi di amministratore.

Ogni estensione ha un OID (Object Identifier) che la identifica in modo univoco. In questo caso, l'estensione `Role` ha l'OID `1.3.6.1.4.1.99999.1`, mentre l'estensione `DeviceId` ha l'OID `1.3.6.1.4.1.99999.2.`

Gli OID indicati appartengono allo spazio degli OID privati (sotto 1.3.6.1.4.1) e in particolare al Private Enterprise Number (PEN) 99999.
Il valore 999999 è stato scelto a caso e non è registrato ufficialmente. In un contesto reale, è consigliabile utilizzare un PEN registrato per evitare conflitti con altri OID.

Per scoprirlo con certezza, si deve consultare il registro PEN ufficiale di IANA: https://pen.iana.org/
-->
---

# Come Mitigare/Bloccare la SDE

- **Problema:** Anche con mTLS, se tutti accedono a tutto, SDE è dietro l'angolo
- **Soluzione:** Usare le info della SecurityIdentity + Augmentor
- **Esempio:** Solo chi ha `ADMIN` o altri specifici ruoli (da certificato) può accedere ai dati sensibili
- **Risultato:** Nessuno vede ciò che non dovrebbe, anche se autenticato

<!--
Anche se mTLS fornisce un'autenticazione forte, se non si implementano controlli di accesso adeguati, la Sensitive Data Exposure (SDE) può ancora verificarsi. La soluzione consiste nell'utilizzare le informazioni contenute nella `SecurityIdentity` e nell'Augmentor per implementare controlli di accesso più granulari.

Ad esempio, si può decidere che solo gli utenti con il ruolo `ADMIN`, estratto dal certificato client, possano accedere a determinate risorse o dati sensibili. In questo modo, anche se un client è autenticato, non avrà accesso a ciò che non gli compete.
-->
---

# Configurazione policy di accesso

```properties
# Section to configure the server for the role-based access control mechanism.
# For more information see Mapping certificate attributes to roles in Quarkus
# https://quarkus.io/guides/security-authentication-mechanisms#map-certificate-attributes-to-roles
# In this case using the `RolesAugmentor` class to map the certificate attributes to roles.
# The custom role as an extension in the client certificate is identified by the custom OID 1.3.6.1.4.1.12345.1
# Whether permission check should be applied on all matching paths, or paths specific for the Jakarta REST resources.
quarkus.http.auth.permission.certauthenticated.applies-to=jaxrs
# The methods that this permission set applies to.
quarkus.http.auth.permission.certauthenticated.methods=GET,POST
# The paths that this permission set applies to.
quarkus.http.auth.permission.certauthenticated.paths=/api/v1/connection-info/*
# The policy to use for the permission check.
quarkus.http.auth.permission.certauthenticated.policy=role-policy-cert
# The roles allowed to access the resource.
quarkus.http.auth.policy.role-policy-cert.roles-allowed=User,Administrator
```

<!--
In Quarkus, è possibile configurare le policy di accesso per controllare chi può accedere a determinate risorse in base ai ruoli. Nella configurazione mostrata, abbiamo definito una policy che si applica a tutte le richieste GET e POST sui percorsi `/api/v1/connection-info/*`. 

La policy richiede che l'utente abbia uno dei ruoli specificati, in questo caso `User` o `Administrator`, per accedere a queste risorse. Questo è un esempio di come si può implementare il controllo degli accessi in modo granulare utilizzando le informazioni estratte dai certificati client.

Lo stesso può essere applicato programmaticamente, ad esempio, per bloccare l'accesso a un endpoint REST in base al ruolo dell'utente usando l'annotazione `@RolesAllowed`.
-->
---
class: text-sm
---

# Esecuzione dei casi di test

1. **Accesso con certificato rilasciato da una CA ammessa ma senza estensioni**
    - Un client tenta l'accesso a  `/api/v1/connection-info/info`
    - **Risultato:** Accesso negato con HTTP 401 Unauthorized
    - **Motivo:** Non sono ammessi certificati senza estensioni anche se firmati da CA ammessa ed Extended Key Usage
      `TLS Web Client Authentication`
2. **Accesso con certificato avente estensione personalizzata `DeviceId` e valore `1234567890`**
    - Un client tenta l'accesso a  `/api/v1/connection-info/info`
    - **Risultato:** Accesso negato con HTTP 401 Unauthorized
    - **Motivo:** Non sono ammessi certificati il cui valore dell'estensione `DeviceId` non è stato validato
      dall'algoritmo di validazione di `OidSecurityIdentityAugmentor`
3. **Accesso con certificato avente estensione personalizzata `Role` e valore `ProjectManager`**
    - Un client tenta l'accesso a  `/api/v1/connection-info/info`
    - **Risultato:** Accesso negato con HTTP 403 Forbidden
    - **Motivo:** Da policy di accesso, il ruolo `ProjectManager` ha l'accesso negato alla risorsa

<!--
Adesso vediamo in azione la PoC con alcuni dei casi di test che mostrano come funziona l'autenticazione mTLS e il controllo degli accessi in Quarkus in base a ciò che abbiamo appena visto.

HTTP 401 Unauthorized significa che l'autenticazione è fallita, mentre HTTP 403 Forbidden significa che l'autenticazione è andata a buon fine, ma l'utente non ha i permessi per accedere alla risorsa richiesta.
-->
---

# Esecuzione dei casi di test

4. **Accesso con certificato avente estensione personalizzata `Role` e valore `User,Administrator`**
    - Un client tenta l'accesso a  `/api/v1/connection-info/info`
    - **Risultato:** Accesso consentito con HTTP 200 OK
    - **Motivo:** Da policy di accesso, il ruolo `User,Administrator` ha l'accesso consentito alla risorsa

<div v-click class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-70 z-50">
    <video src="/images/asciinema/poc_quarkus_mtls_esecuzione_casi_di_test.mp4" controls muted loop width="800"></video>
</div>

---

# Conclusione – La Coppia Perfetta contro la SDE

- **Ricapitolando:**
    - mTLS: identità certa + cifratura
    - Quarkus Security + Augmentor: arricchimento identità
    - Controlli Precisi: blocco mirato e consapevole
- **Vantaggio:** Protezione efficace contro la Sensitive Data Exposure
- **Consiglio:** Se gestisci dati sensibili, usa mTLS + Augmentor per dormire sonni tranquilli!

<!--
In sintesi, l'uso di mTLS insieme al sistema di sicurezza di Quarkus e all'Augmentor consente di creare un sistema di autenticazione e autorizzazione robusto e sicuro. Questo approccio non solo garantisce che le comunicazioni siano protette, ma fornisce anche un controllo granulare su chi può accedere a cosa.

In questo modo, si può prevenire la Sensitive Data Exposure (SDE) e garantire che solo i client autorizzati possano accedere a risorse sensibili. Se gestisci dati sensibili, ti consiglio vivamente di considerare l'implementazione di mTLS e dell'Augmentor di Quarkus per garantire la massima sicurezza.
-->
---

# Risorse Utili

Ecco alcune risorse utili per approfondire gli argomenti trattati:

- **Quarkus Security Documentation:** [https://quarkus.io/guides/security-overview](https://quarkus.io/guides/security-overview)
- **Quarkus mTLS Guide:** [https://quarkus.io/guides/security-authentication-mechanisms#mutual-tls-mtls-authentication](https://quarkus.io/guides/security-authentication-mechanisms#mutual-tls-mtls-authentication)
- **Quarkus TLS Registry Reference:** [https://quarkus.io/guides/tls-registry-reference](https://quarkus.io/guides/tls-registry-reference)
- **Articolo Blog sull'implementazione mTLS con Quarkus:** [https://bit.ly/3MQPA3v](https://bit.ly/3MQPA3v) (dal
  progetto `amusarra/quarkus-mtls-auth`)
- **OWASP Sensitive Data Exposure (A02:2021):** [https://owasp.org/Top10/A02_2021-Cryptographic_Failures/](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/) (
  Nota: SDE era A3:2017, ora parte di A02:2021)

---

# About Me

- **Nome:** [Antonio Musarra]
- **Professione:** [Software Engineer@SOGEI, Tech Blogger]
- **Località:** [Roma]

## Contatti

- **Email:** [amusarra@sogei.it]
- **LinkedIn:** [https://www.linkedin.com/in/amusarra/]
- **My GitHub Repositories**: [https://github.com/amusarra]
- **Business Site**: [https://www.sogei.it]
- **My Blog**: [https://www.dontesta.it]

<div class="top-70% absolute right--1% flex w-70">
  <div class="flex flex-col items-center w-1/2">
    <img src="/images/qr_code_to_slide.png" width="60%">
    <p class="text-xs">Link to Slide</p>
  </div>
  <div class="flex flex-col items-center w-1/2">
    <img src="/images/qr_code_link_to_code.png" width="60%">
    <p class="text-xs">Link to GitHub</p>
  </div>
</div>

<div class="top-5% absolute left-75%">
    <img src="/images/foto_profilo.png" width="70%" height="70%">
</div>
---

# Domande?

Grazie per l'attenzione! Se avete domande, sono qui per rispondere.
