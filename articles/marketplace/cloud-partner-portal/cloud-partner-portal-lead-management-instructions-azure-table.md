---
title: Istruzioni relative alla gestione dei lead per le tabelle di Azure in Azure Marketplace | Microsoft Docs
description: Questo articolo illustra le istruzioni dettagliate per configurare la gestione dei lead con l&quot;archiviazione tabelle di Azure.
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 45aa2e0b178969215df9ae305d0a13a350ae0fda
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-azure-table"></a>Istruzioni relative alla gestione dei lead per le tabelle di Azure

Questo documento illustra come configurare le tabelle di Azure in modo che Microsoft possa fornire lead di vendita. Le tabelle di Azure sono la scelta ottimale se si desidera personalizzare il modo in cui archiviare i propri lead.

1. Se non si dispone di un account Azure, è prima necessario [crearne uno](https://azure.microsoft.com/pricing/free-trial/).

2. Quando l'account Azure è pronto, accedere al [portale di Azure](https://portal.azure.com) e creare un account di archiviazione. Vedere la schermata seguente per istruzioni e fare clic qui per [altre informazioni sui prezzi di Archiviazione](https://azure.microsoft.com/pricing/details/storage/). <br/>
  ![Immagine del flusso di creazione di Archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

3. Copiare quindi la stringa di connessione per la chiave e incollarla nel campo <b>Stringa di connessione a account di archiviazione</b> nel portale per Cloud Partner. <br/> 
  ![Immagine della chiave di archiviazione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

  Stringa di connessione finale di esempio: <br/>
`{"connectionString":"DefaultEndpointsProtocol=https;AccountName=leadaccount;AccountKey=ObS0EW7tDmXrC8oNeG6IRHpx2IUioBQTQynQcR/MUMqrNqQ/RC6zctP8HfucNJO+ond7dJHTROO9ziiPNspjEg=="}`

È possibile utilizzare [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) (app di terze parti) o un qualsiasi altro strumento per visualizzare i dati nella tabella di archiviazione o esportare i dati.

### <a name="optional-azure-functions--azure-table"></a>**(Facoltativo)**  Funzioni di Azure + Tabella di Azure
Se si desidera personalizzare il modo in cui si ricevono questi lead, il servizio [Funzioni di Azure](https://azure.microsoft.com/services/functions/) offre la massima flessibilità per automatizzare il processo di generazione di lead. Di seguito è riportato un esempio di creazione di una funzione di Azure con un timer che viene eseguita ogni cinque minuti, ricerca nuovi record in una tabella di Azure e utilizza il livello gratuito del servizio SendGrid per inviare una semplice notifica tramite posta elettronica.

1. [Creare](https://portal.azure.com/#create/SendGrid.SendGrid) un account del servizio SendGrid gratuito nella sottoscrizione di Azure.
  
    ![Creazione di un account SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)
  
2. Creare una chiave API di SendGrid. A tale scopo fare clic su Gestisci chiave per accedere all'interfaccia utente di SendGrid, scegliere Impostazioni, Chiavi API e creare una chiave con l'invio di posta elettronica impostato su Accesso completo, quindi salvare la chiave API.
 
    ![Chiave API di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)

3. [Creare](https://portal.azure.com/#create/Microsoft.FunctionApp) un'app per le funzioni di Azure usando l'opzione di piano di hosting denominata "Piano A consumo". 
  
    ![Creazione di una funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)

4. Creare una nuova definizione di funzione. 
  
    ![Creazione di una definizione di funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)

5. Se si desidera che tramite la funzione venga inviato un aggiornamento in un momento specifico, selezionare TimerTrigger-CSharp come opzione di attivazione.
  
    ![Opzione di attivazione a tempo della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)
  
6. Sostituire il codice di "Sviluppo" con quello della tabella seguente. Sarà necessario modificare gli indirizzi di posta elettronica in modo che corrispondano a quelli del mittente e del destinatario.
Quello che segue è solo un esempio, che può essere eventualmente modificato per migliorare il codice.
  
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

7. Fare clic su "Integrazione" e "Input" per definire la connessione della tabella di Azure:
  
    ![Integrazione della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)

8. Immettere il nome della tabella e definire la stringa di connessione facendo clic su "nuova".
  
    ![Connessione della tabella della funzione di Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9. Definire ora l'output come SendGrid e mantenere tutti i valori predefiniti.
  
    ![Output di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)
    ![Valori predefiniti dell'output di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Aggiungere la chiave API di SendGrid in Impostazioni dell'app per le funzioni usando il nome "SendGridApiKey" e il valore ottenuto da Chiavi API nell'interfaccia utente di SendGrid
  
    ![Gestione di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![Gestione chiavi di SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Al termine della configurazione, la sezione Integrazione dovrebbe contenere codice simile al seguente:

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
  
Infine è necessario tornare all'interfaccia utente di Sviluppo e fare clic su **Esegui** per avviare il timer. Ora è tutto pronto per ricevere una notifica ogni volta che si rende disponibile un nuovo lead.

