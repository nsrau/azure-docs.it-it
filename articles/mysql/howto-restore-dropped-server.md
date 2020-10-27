---
title: Ripristinare un database di Azure per il server MySQL eliminato
description: Questo articolo descrive come ripristinare un server eliminato nel database di Azure per MySQL usando il portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 982747c1a7e093f84daeb63e75cfdf439d3fccf9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546722"
---
# <a name="restore-a-dropped-azure-database-for-mysql-server"></a>Ripristinare un database di Azure per il server MySQL eliminato

Quando un server viene eliminato, il backup del server di database può essere conservato fino a cinque giorni nel servizio. È possibile accedere al backup del database e ripristinarlo solo dalla sottoscrizione di Azure in cui risiedeva originariamente il server. È possibile seguire la procedura consigliata di seguito per ripristinare una risorsa di MySQL Server eliminata entro 5 giorni dal momento dell'eliminazione del server. I passaggi consigliati funzioneranno solo se il backup per il server è ancora disponibile e non è stato eliminato dal sistema. 

## <a name="pre-requisites"></a>Prerequisiti
Per ripristinare un database di Azure per il server MySQL, è necessario quanto segue:
- Nome della sottoscrizione di Azure che ospita il server originale
- Percorso in cui è stato creato il server

## <a name="steps-to-restore"></a>Passaggi da ripristinare

1. Passare al [log attività](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) dal pannello monitoraggio in portale di Azure. 

2. Nel log attività fare clic su **Aggiungi filtro** come illustrato e impostare i filtri seguenti per il 

    - **Subscription** = sottoscrizione che ospita il server eliminato
    - **Tipo di risorsa** : database di Azure per i server MySQL (Microsoft. DBforMySQL/Servers) 
    - **Operazione** = Elimina server MySQL (Microsoft. DBforMySQL/Servers/Delete) 
 
     [![Log attività filtrato per l'operazione di eliminazione del server MySQL](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Fare doppio clic sull'evento Delete MySQL server e fare clic sulla scheda JSON e prendere nota degli attributi "resourceId" e "submissionTimestamp" nell'output JSON. Il resourceId è nel formato seguente:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. Passare alla [pagina Crea API REST del server](/rest/api/mysql/servers/create) e fare clic sulla scheda "prova" evidenziata in verde e accedere con l'account Azure.
 
 5. Specificare resourceGroupName, serverName (nome server eliminato), subscriptionId, derivato dall'attributo resourceId acquisito nel passaggio 3, mentre API-Version è già popolata come illustrato nell'immagine.
 
     [![Creare un server con l'API REST](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
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

7. Se viene visualizzato il codice di risposta 201 o 202, la richiesta di ripristino viene inviata correttamente. 

8. La creazione del server può richiedere tempo a seconda delle dimensioni del database e delle risorse di calcolo di cui viene eseguito il provisioning nel server originale. Lo stato del ripristino può essere monitorato dal log attività filtrando 
   - **Sottoscrizione** = sottoscrizione
   - **Tipo di risorsa** : database di Azure per i server MySQL (Microsoft. DBforMySQL/Servers) 
   - **Operazione** = aggiornamento del server MySQL creato

## <a name="next-steps"></a>Passaggi successivi
- Se si sta tentando di ripristinare un server entro cinque giorni e viene comunque visualizzato un errore dopo aver eseguito accuratamente i passaggi descritti in precedenza, aprire una richiesta di assistenza per assistenza. Se si sta tentando di ripristinare un server eliminato dopo cinque giorni, è previsto un errore poiché non è possibile trovare il file di backup. Non aprire un ticket di supporto in questo scenario. Il team di supporto non può fornire assistenza se il backup viene eliminato dal sistema. 
- Per evitare l'eliminazione accidentale di server, è consigliabile usare [blocchi di risorse](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).