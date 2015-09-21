<properties
   pageTitle="Novità del Catalogo dati di Azure"
   description="Panoramica delle nuove funzionalità nell'anteprima del Catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/04/2015"
   ms.author="maroche"/>

# Novità del Catalogo dati di Azure

Gli aggiornamenti sul **Catalogo dati di Azure** vengono rilasciati a intervalli regolari. Non tutte le nuove versioni includeranno nuove funzionalità destinate all'utente, in quanto alcune versioni sono incentrate sulle funzionalità del servizio back-end. Questa pagina illustra le nuove funzionalità destinate all'utente aggiunte al servizio **Catalogo dati di Azure**.

## Novità della versione della settimana del 4 settembre 2015

Nella settimana del 4 settembre 2015, sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per la registrazione manuale di tipi di origini dati sconosciute. Gli utenti possono immettere manualmente le informazioni sull’origine dati utilizzando il portale **Catalogo dati di Azure** in modo che le origini dati non esplicitamente supportate dallo strumento di registrazione origine dati possono essere annotate ed individuate.
- Supporto per la registrazione e l'individuazione dei database di SQL Server come contenitori. Quando si registrano le tabelle di SQL Server e le viste, **Catalogo dati di Azure** crea una voce per il database, le tabelle e le viste. Il database può essere individuato e annotato utilizzando il portale **Catalogo dati di Azure**. Gli utenti possono inoltre cercare e filtrare il contenuto di un database oltre a cercare e filtrare il contenuto del catalogo.


> [AZURE.NOTE]Le tabelle di SQL Server e le viste che sono state registrate prima della versione del 04-09-2015 devono essere registrate utilizzando lo strumento di registrazione di origine dati prima di aggiungere la voce del database nel catalogo. La nuova registrazione di un'origine dati non influisce sulle annotazioni aggiunte dagli utenti nel portale **Catalogo dati di Azure**.

## Novità della versione della settimana del 28 agosto 2015

Nella settimana del 28 agosto 2015, sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per l'analisi dei dati di origini dati di SQL Server e Oracle. Quando si registrano le viste e le tabelle di SQL Server e Oracle, gli utenti possono scegliere di includere informazioni sul profilo dei dati per gli oggetti da registrare. Il profilo dei dati include le statistiche a livello di oggetto e a livello di colonna.
- Supporto per le origini dati HDFS Hadoop. Gli utenti possono ora registrarsi e individuare directory e file HDFS.

## Novità della versione della settimana del 21 agosto 2015

Nella settimana del 21 agosto 2015, sono state aggiunte al **Catalogo dati di Azure** le funzionalità seguenti:

- Supporto per fornire informazioni sulla richiesta di accesso per le origini dati registrate. Per qualsiasi asset di dati registrato, gli utenti possono ora fornire istruzioni per richiedere l'accesso, inclusi URL o collegamenti di posta elettronica, per una facile integrazione con i processi e gli strumenti esistenti.
- Descrizioni comandi per i tag e gli esperti, per semplificare l'individuazione dei metadati forniti dagli utenti per gli asset di dati registrati.
- Nuovo menu e pulsante "User" nella barra di spostamento superiore. Questo menu consente di vedere l'account usato per accedere al **Catalogo dati di Azure** e di disconnettersi se lo si desidera. Questo menu visualizza inoltre il nome del catalogo, utile per gli sviluppatori che usano l'API REST del **Catalogo dati di Azure**.
- Edizione Standard: quando si aggiungono proprietari agli asset di dati, il **Catalogo dati di Azure** ora supporta gli account utente e i gruppi di sicurezza come proprietari. Per aggiungere un gruppo di sicurezza come proprietario di asset di dati selezionati, è possibile immettere il nome visualizzato o l'indirizzo di posta elettronica UPN del gruppo, se presente.
- Supporto per le origini dati dell'archivio BLOB di Azure. Gli utenti possono ora registrarsi e individuare i BLOB e le directory di archiviazione di Azure.

<!---HONumber=Sept15_HO2-->