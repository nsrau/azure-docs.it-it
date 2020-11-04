---
title: Ripristinare un database di Azure per il server PostgreSQL eliminato
description: Questo articolo descrive come ripristinare un server eliminato nel database di Azure per PostgreSQL usando il portale di Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 81764294cc29ad74d5a77f2055f10498d69b59e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343115"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Ripristinare un database di Azure per il server PostgreSQL eliminato

Quando un server viene eliminato, il backup del server di database può essere conservato fino a cinque giorni nel servizio. È possibile accedere al backup del database e ripristinarlo solo dalla sottoscrizione di Azure in cui risiedeva originariamente il server. È possibile seguire questa procedura consigliata per ripristinare una risorsa server PostgreSQL eliminata entro 5 giorni dal momento dell'eliminazione del server. I passaggi consigliati funzioneranno solo se il backup per il server è ancora disponibile e non è stato eliminato dal sistema. 

## <a name="pre-requisites"></a>Prerequisiti
Per ripristinare un database di Azure per il server PostgreSQL eliminato, è necessario quanto segue:
- Nome della sottoscrizione di Azure che ospita il server originale
- Percorso in cui è stato creato il server

## <a name="steps-to-restore"></a>Passaggi da ripristinare

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selezionare il servizio **monitoraggio di Azure** , quindi selezionare **log attività**.

2. Nel log attività fare clic su **Aggiungi filtro** come illustrato e impostare i filtri seguenti per i seguenti elementi

    - **Subscription** = sottoscrizione che ospita il server eliminato
    - **Tipo di risorsa** = database di Azure per server PostgreSQL (Microsoft. DBforPostgreSQL/Servers)
    - **Operazione** = Elimina server PostgreSQL (Microsoft. DBforPostgreSQL/Servers/Delete)
 
    ![Log attività filtrato per l'operazione di eliminazione del server PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selezionare l'evento **delete server PostgreSQL** , quindi selezionare la **scheda JSON**. Copiare gli `resourceId` `submissionTimestamp` attributi e nell'output JSON. Il resourceId è nel formato seguente: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Passare alla pagina dell' [API REST](/rest/api/PostgreSQL/servers/create) per la creazione del server PostgreSQL e selezionare la scheda **try it** evidenziata in verde. Accedere con l'account Azure.

 5. Specificare le proprietà **resourceGroupName** , **ServerName** (nome server eliminato) e **SubscriptionId** , in base al valore JSON dell'attributo resourceID acquisito nel passaggio 3 precedente. La proprietà API-Version è già popolata e può essere lasciata così com'è, come illustrato nella figura seguente.

    ![Creare un server con l'API REST](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Scorrere sotto la sezione corpo della richiesta e incollare il codice seguente sostituendo il "percorso del server eliminato", "submissionTimestamp" e "resourceId". Per "restorePointInTime", specificare il valore "submissionTimestamp" meno **15 minuti** per verificare che il comando non venga eliminato.
    ```json
        {
          "location": "Dropped Server Location",  
          "properties": 
              {
                  "restorePointInTime": "submissionTimestamp - 15 minutes",
                  "createMode": "PointInTimeRestore",
                  "sourceServerId": "resourceId"
            }
        }
    ```
    Ad esempio, se l'ora corrente è 2020-11-02T23:59:59.0000000 Z, è consigliabile un minimo di 15 minuti prima del punto di ripristino nel tempo 2020-11-02T23:44:59.0000000 Z.
    > [!Important]
    > È previsto un limite di tempo di cinque giorni dopo l'eliminazione del server. Dopo cinque giorni, è previsto un errore poiché non è possibile trovare il file di backup.
    
7. Se viene visualizzato il codice di risposta 201 o 202, la richiesta di ripristino viene inviata correttamente. 

    La creazione del server può richiedere tempo a seconda delle dimensioni del database e delle risorse di calcolo di cui viene eseguito il provisioning nel server originale. Lo stato del ripristino può essere monitorato dal log attività filtrando 
   - **Sottoscrizione** = sottoscrizione
   - **Tipo di risorsa** = database di Azure per server PostgreSQL (Microsoft. DBforPostgreSQL/Servers) 
   - **Operazione** = aggiornamento del server PostgreSQL creato

## <a name="next-steps"></a>Passaggi successivi
- Se si sta tentando di ripristinare un server entro cinque giorni e viene comunque visualizzato un errore dopo aver eseguito accuratamente i passaggi descritti in precedenza, aprire una richiesta di assistenza per assistenza. Se si sta tentando di ripristinare un server eliminato dopo cinque giorni, è previsto un errore poiché non è possibile trovare il file di backup. Non aprire un ticket di supporto in questo scenario. Il team di supporto non può fornire assistenza se il backup viene eliminato dal sistema. 
- Per evitare l'eliminazione accidentale di server, è consigliabile usare [blocchi di risorse](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
