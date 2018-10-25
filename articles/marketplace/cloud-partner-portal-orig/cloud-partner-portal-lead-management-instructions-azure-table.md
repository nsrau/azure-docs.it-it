---
title: Tabella di Azure | Microsoft Docs
description: Configurare la gestione dei clienti potenziali per le tabelle di Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808466"
---
<a name="lead-management-instructions-for-azure-table"></a>Istruzioni relative alla gestione dei lead per le tabelle di Azure
============================================

Questo articolo descrive come configurare le tabelle di Azure per l'archiviazione dei clienti potenziali. La tabella di Azure consente di archiviare e personalizzare le informazioni dei clienti.

## <a name="to-configure-azure-table"></a>Per configurare la tabella di Azure

1.  Se non si ha un account Azure, è possibile [creare un account di prova gratuito](https://azure.microsoft.com/pricing/free-trial/).

2.  Quando l'account di Azure è attivo, accedere al [portale di Azure](https://portal.azure.com).
3.  Creare un account di archiviazione nel portale di Azure. La schermata successiva spiega come creare un account di archiviazione. Altre informazioni sui prezzi delle risorse di archiviazione sono disponibili [qui](https://azure.microsoft.com/pricing/details/storage/).

    ![Procedura di creazione di un account di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copiare la stringa di connessione dell'account di archiviazione per la chiave e incollarla nel campo **Stringa di connessione a account di archiviazione** nel portale Cloud Partner. Un esempio di stringa di connessione è `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Chiave di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

È possibile usare lo [strumento di esplorazione dell'archiviazione di Azure](http://azurestorageexplorer.codeplex.com/) o qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione. È anche possibile esportare i dati nella tabella di Azure.
dati.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Facoltativo)**  Per usare Funzioni di Azure con una tabella di Azure

Se si vuole personalizzare il modo in cui si ricevono i clienti potenziali, usare [Funzioni di Azure](https://azure.microsoft.com/services/functions/) con una tabella di Azure. Il servizio Funzioni di Azure consente di automatizzare il processo di generazione dei clienti potenziali.

La procedura seguente illustra come creare una funzione di Azure che usa un timer. Ogni cinque minuti, la funzione cerca nuovi record nella tabella di Azure e quindi usa il servizio SendGrid per inviare una notifica di posta elettronica.


1.  [Creare](https://portal.azure.com/#create/SendGrid.SendGrid) un account del servizio SendGrid gratuito nella sottoscrizione di Azure.

    ![Creazione di un account SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Creare una chiave API di SendGrid 
    - Selezionare **Gestisci** per passare all'interfaccia utente di SendGrid
    - Selezionare **Impostazioni**, **Chiavi API**, quindi creare una chiave impostando Mail Send (Invio di posta elettronica) -\> Accesso completo
    - Salvare la chiave API


    ![Chiave API di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Creare](https://portal.azure.com/#create/Microsoft.FunctionApp) un'app per le funzioni di Azure usando l'opzione di piano di hosting denominata "Piano A consumo".

    ![Creare un'app per le funzioni di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Creare una nuova definizione di funzione.

    ![Creare una definizione di funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Per ottenere la funzione di invio di un aggiornamento in un momento specifico, selezionare **TimerTrigger-CSharp** come opzione di attivazione.

     ![Opzione di attivazione a tempo della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Sostituire il codice "Develop" con l'esempio di codice seguente. Modificare gli indirizzi di posta elettronica con gli indirizzi da usare per mittente e destinatario.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Frammento di codice della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Selezionare **Integrazione** e **Input** per definire la connessione della tabella di Azure.

    ![Integrazione della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Immettere il nome della tabella e definire la stringa di connessione selezionando **nuova**.


    ![Connessione della tabella della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Definire ora l'output come SendGrid e mantenere tutti i valori predefiniti.

    ![Output di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Impostazioni predefinite dell'output di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Aggiungere la chiave API di SendGrid alle impostazioni dell'app per le funzioni usando il nome "SendGridApiKey" e il valore ottenuto dalle chiavi API nell'interfaccia utente di SendGrid

    ![Gestione di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![Gestione chiavi di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Dopo aver completato la configurazione della funzione, il codice nella sezione di integrazione deve essere simile all'esempio seguente.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. Come passaggio finale, passare all'interfaccia utente di sviluppo della funzione e quindi selezionare **Esegui** per avviare il timer. Ora si riceverà una notifica ogni volta che arriva un nuovo cliente potenziale.
