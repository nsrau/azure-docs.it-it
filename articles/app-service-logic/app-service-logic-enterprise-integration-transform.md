<properties 
	pageTitle="Panoramica di Enterprise Integration Pack | Servizio app di Microsoft Azure | Microsoft Azure" 
	description="Usare le funzionalità di Enterprise Integration Pack per abilitare processi aziendali e scenari di integrazione tramite il Servizio app di Microsoft Azure" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Enterprise Integration con trasformazioni XML

## Panoramica
Il connettore di trasformazione di Enterprise Integration converte i dati da un formato a un altro. Ad esempio, si è ricevuto un messaggio contenente la data corrente nel formato YearMonthDay. È possibile usare una trasformazione per riformattare la data nel formato MonthDayYear.

## Funzioni della trasformazione
Un'app per le API Transform, conosciuta anche come Map, consiste in uno schema XML di origine (input) e in uno schema XML di destinazione (output). È possibile usare diverse funzioni predefinite per contribuire a gestire o controllare i dati, incluse la modifica di stringhe, le operazioni Conditional Assignment, le espressioni aritmetiche, i formattatori di data/ora e persino i costrutti a ciclo continuo.

## Procedura: Creare una trasformazione
È possibile creare una mappa/trasformazione tramite l'[SDK di Enterprise Integration](https://aka.ms/vsmapsandschemas) di Visual Studio. Al termine della creazione e del testing della trasformazione, caricare la trasformazione nell'account di integrazione.

## Procedura: Usare una trasformazione
Dopo aver caricato il file della trasformazione nell'account di integrazione, è possibile usarlo per creare un'app per la logica. L'app per la logica eseguirà quindi le trasformazioni a ogni attivazione dell'app, con la relativa trasformazione del contenuto immesso.

**Di seguito è riportata la procedura per l'uso di una trasformazione**:

### Prerequisiti 
Nell'anteprima è necessario:

-  [Creare un contenitore di Funzioni di Azure](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Creare un contenitore di Funzioni di Azure")
-  [Aggiungere una funzione al contenitore di Funzioni di Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Questo modello consente di creare una funzione webhook basata sulla funzione di Azure C# con funzionalità di trasformazione da usare negli scenari di integrazione di app per la logica")
-  Creare un account di integrazione e aggiungervi una mappa

>[AZURE.TIP] Annotare il nome del contenitore di Funzioni di Azure e della funzione di Azure, che saranno necessari nel passaggio successivo.

Una volta soddisfatti i requisiti, è ora di creare l'app per la logica:

1. Creare un'app per la logica e [collegarla all'account di integrazione](./app-service-logic-enterprise-integration-accounts.md "Informazioni su come collegare un account di integrazione a un'app per la logica") che contiene la mappa.
2. Aggiungere un trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica ![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. Aggiungere l'azione **Transform XML** selezionando prima **Aggiungi un'azione** ![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. Immettere la parola *transform* nella casella di ricerca per filtrare tutte le azioni su quella che si vuole usare ![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. Selezionare l'azione **Transform XML** ![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. Selezionare il **Contenitore funzioni** con la funzione da usare. Si tratta del nome del contenitore di Funzioni di Azure creato in precedenza.
7. Selezionare la **FUNZIONE** che si vuole usare. Si tratta del nome della funzione di Azure creato in precedenza.
8. Aggiungere il **CONTENUTO** XML da trasformare. Si noti che è possibile usare tutti i dati XML ricevuti nella richiesta HTTP come **CONTENUTO**. In questo esempio selezionare il corpo della richiesta HTTP che ha attivato l'app per la logica.
9. Selezionare il nome della **MAPPA** che si vuole usare per eseguire la trasformazione. La mappa deve essere già presente nell'account di integrazione. In un passaggio precedente è stato concesso l'accesso dell'app per la logica all'account di integrazione che contiene la mappa.
10. Salvare il lavoro ![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

A questo punto, la configurazione della mappa è completa. In un'applicazione reale è possibile archiviare i dati trasformati in un'applicazione LOB, ad esempio SalesForce. È possibile eseguire facilmente questa azione inviando l'output della trasformazione a Salesforce.

È ora possibile testare la trasformazione eseguendo una richiesta all'endpoint HTTP.

## Funzionalità e casi d'uso

- La trasformazione creata in una mappa può essere semplice, come ad esempio la copia di un nome e di un indirizzo da un documento a un altro. In alternativa, è possibile creare trasformazioni più complesse usando operazioni di mapping predefinite.
- Sono già disponibili più operazioni o funzioni di mapping, incluse stringhe, funzioni data/ora e così via.
- È possibile eseguire una copia diretta dei dati tra gli schemi. Nel mapper incluso nell'SDK, questa operazione è semplice quanto tracciare una linea che colleghi gli elementi nello schema di origine con le relative controparti nello schema di destinazione.
- Durante la creazione di una mappa viene visualizzata la relativa rappresentazione grafica, che mostra tutte le relazioni e i collegamenti creati dall'utente.
- Usare la funzione Mappa di test per aggiungere un messaggio XML di esempio. Con un semplice clic, è possibile testare la mappa creata e visualizzare l'output generato.
- Caricare le mappe esistenti
- Include il supporto per il formato XML.


## Altre informazioni
- [Altre informazioni su Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")
- [Altre informazioni sulle mappe](./app-service-logic-enterprise-integration-maps.md "Informazioni sulle mappe di Enterprise Integration")
 

<!---HONumber=AcomDC_0727_2016-->