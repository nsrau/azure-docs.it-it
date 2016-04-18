<properties
   pageTitle="API .NET di Power BI Embedded"
   description="API .NET di Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# API .NET di Power BI Embedded

La versione di anteprima di **Microsoft Power BI Embedded** illustra per lo più le principali funzionalità disponibili dell'API Power BI, considerata parte di un servizio di Azure per lo sviluppo delle applicazioni. Sarà possibile imparare inoltre a eseguire il provisioning, sviluppare e distribuire il contenuto di Power BI e le risorse necessarie a livello di codice.

L'**API Power BI** può essere usata per creare e gestire le aree di lavoro dei contenuti di Power BI. Con l'API, è possibile

  - Importare un file PBIX di Power BI Desktop in un'area di lavoro usando l'autenticazione basata su chiavi.
  - Modificare le stringhe di connessione di un set di dati.
  - Ottenere **report** da integrare nella propria app.
  - Impostare le credenziali di un set di dati per poter comunicare con l'origine dati corretta.
  - Per altre informazioni sull'**API Power BI**, vedere [Introduzione alla versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md).

Vedere i [riferimenti a Power BI su MSDN](https://msdn.microsoft.com/library/mt669800.aspx).

Di seguito alcune delle classi e dei metodi usati nel **esempio di Power BI Embedded**:

## Spazio dei nomi Microsoft.PowerBI.Api.Beta

### Classe ReportsExtensions
|Nome|Descrizione
|---|---
|[GetReports(IReports, String, String)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreports.aspx)|Ottiene un elenco di report disponibili nell'area di lavoro specificata
|[GetReportsAsync(IReports, String, String, CancellationToken)](https://msdn.microsoft.com/library/microsoft.powerbi.api.beta.reportsextensions.getreportsasync.aspx)|Ottiene un elenco di report disponibili nell'area di lavoro specificata

## Spazio dei nomi Microsoft.PowerBI.Security

### Metodi PowerBIToken
|Nome| Descrizione
|---|---
|[CreateDevToken(String, Guid)](https://msdn.microsoft.com/library/mt670215.aspx)|Crea un token sviluppatore con scadenza predefinita, usato per accedere ai servizi di piattaforma Power BI
|[CreateProvisionToken(String)](https://msdn.microsoft.com/library/mt670218.aspx)|Crea un token di provisioning con scadenza predefinita, usato per gestire le aree di lavoro all'interno di una raccolta di aree di lavoro

## Vedere anche

- [Cos'è Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Introduzione alla versione di anteprima di Microsoft Power BI Embedded](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0406_2016-->