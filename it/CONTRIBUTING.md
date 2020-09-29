# Contribuire alle specifiche TDEX

Questo file definisce le linee guida e il flusso di lavoro da seguire per contribuire correttamente a questo progetto.

## Specifiche di scrittura

La specifica dovrebbe essere leggibile sia in formato [Markdown](https://en.wikipedia.org/wiki/Markdown) che in testo normale.

Some requirements are obvious, some are subtle. They're designed to walk an implementer through the code they have to write, so write them as YOU develop YOUR implementation. Stick with MUST/SHOULD/MAY and NOT: see [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt)

## Estendi i messaggi

Tutti i payload dei messaggi sono serializzati con Protcol Buffers, al fine di aggiungere nuovi campi o deprecare quelli più vecchi DEVI aumentare il numero per mantenere la compatibilità con le versioni precedenti.

## Creazione di vettori di prova

Per le nuove costruzioni di protocolli di basso livello, sono necessari vettori di test. Queste sono state tradizionalmente linee all'interno della specifica stessa, ma la tendenza moderna è quella di utilizzare JSON e file separati. L'intento è che siano leggibili dalla macchina dalle implementazioni.

## Flusso di lavoro

Per contribuire con una patch, il flusso di lavoro è il seguente:

1. Repository di fork
2. Crea ramo argomento
3. Effettua il commit delle patch
4. Esegui il push delle modifiche alla forcella
5. Invia una richiesta pull

Linee guida:

- I commit dovrebbero essere atomici e di facile lettura, i messaggi dovrebbero essere descritti in dettaglio per aiutare i lettori
- Se il commit fa riferimento a un altro problema, aggiungi il riferimento nel messaggio
- Scrivere (se possibile) unit test e / o test funzionali per verificare le patch
- I test esistenti devono essere funzionanti

## Schiacciare

Il committer dovrebbe eliminare o ribasare i commit prima che venga unito, seguendo il [documento](https://git-scm.com/docs/git-rebase) Git.
