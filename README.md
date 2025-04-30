# Autenticazione mTLS e Controllo degli Accessi con Quarkus

Questa presentazione illustra come l'autenticazione mTLS (Mutual TLS), implementata con Quarkus, contribuisca in modo significativo alla prevenzione della Sensitive Data Exposure (SDE). Attraverso una Proof of Concept, viene dimostrato come il `SecurityIdentityAugmentor` possa arricchire l'identità del client con informazioni aggiuntive estratte dai certificati, permettendo un controllo degli accessi più granulare.

## Argomenti trattati

- Introduzione a mTLS e suo funzionamento
- Architettura di sicurezza di Quarkus
- Utilizzo di `SecurityIdentityAugmentor` per estrarre ruoli dai certificati
- Configurazione di policy di accesso
- Dimostrazione pratica con casi di test reali

## Avvio della presentazione

Per avviare la presentazione, puoi utilizzare yarn:

```bash
# Installa le dipendenze
yarn install

# Avvia l'ambiente di sviluppo
yarn dev
```

La presentazione sarà disponibile all'indirizzo: <http://localhost:3030>

Puoi modificare il file `slides.md` per personalizzare il contenuto della presentazione. Grazie al live reload, le modifiche saranno visibili immediatamente nel browser.

## Autore

- **Nome:** Antonio Musarra
- **LinkedIn:** [https://www.linkedin.com/in/amusarra/](https://www.linkedin.com/in/amusarra/)
- **GitHub:** [https://github.com/amusarra](https://github.com/amusarra/)
- **Blog:** [https://www.dontesta.it](https://www.dontesta.it)

## Requisiti

- Node.js
- Yarn (o altro package manager come npm o pnpm)

Per maggiori informazioni su Slidev, consulta la [documentazione ufficiale](https://sli.dev/).