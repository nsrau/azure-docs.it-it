---
title: Problemi noti relativi alla conformità del protocollo SCIM (System for Cross-Domain Identity Management) 2,0-Azure AD
description: Risoluzione dei problemi comuni di compatibilità con il protocollo riscontrati durante l'aggiunta in Azure AD di un'applicazione non inclusa nella raccolta che supporta SCIM 2.0
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: da458b8aaf1ace7b87e98ded59a4bf90e4158e0f
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054087"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Problemi noti e risolti con la conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD

Azure Active Directory (Azure AD) può effettuare automaticamente il provisioning di utenti e gruppi in qualsiasi applicazione o sistema gestito da un servizio Web con interfaccia definita nella [specifica del protocollo System for Cross-Domain Identity Management (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Il supporto del protocollo SCIM 2.0 da parte di Azure AD è descritto in [Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni](use-scim-to-provision-users-and-groups.md), in cui sono indicate le parti specifiche del protocollo che vengono implementate per effettuare automaticamente il provisioning di utenti e gruppi da Azure AD alle applicazioni che supportano SCIM 2.0.

In questo articolo sono descritti i problemi correnti e passati relativi alla conformità del servizio di provisioning utenti di Azure AD con il protocollo SCIM 2.0 e sono fornite indicazioni su come risolverli.

## <a name="understanding-the-provisioning-job"></a>Informazioni sul processo di provisioning
Il servizio di provisioning usa il concetto di processo per operare su un'applicazione. JobID si trova nell' [indicatore di stato](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Tutte le nuove applicazioni di provisioning vengono create con un jobID che inizia con "scim". Il processo scim rappresenta lo stato corrente del servizio. I processi precedenti hanno l'ID "customappsso". Questo processo rappresenta lo stato del servizio in 2018. 

Se si usa un'applicazione nella raccolta, il processo contiene in genere il nome dell'app, ad esempio zoom fiocco di neve, databricks e così via. È possibile ignorare questa documentazione quando si usa un'applicazione della raccolta. Questo vale principalmente per le applicazioni non della raccolta con jobID SCIM o customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>Problemi di conformità con SCIM 2.0 e stato
Nella tabella seguente, qualsiasi elemento contrassegnato come Fixed significa che il comportamento corretto è reperibile nel processo SCIM. Abbiamo lavorato per garantire la compatibilità con le versioni precedenti per le modifiche apportate. Tuttavia, non è consigliabile implementare il comportamento precedente. È consigliabile usare il nuovo comportamento per tutte le nuove implementazioni e aggiornare le implementazioni esistenti.

> [!NOTE]
> Per le modifiche apportate in 2018, è possibile ripristinare il comportamento di customappsso. Per le modifiche apportate a partire dalla 2018, è possibile usare gli URL per ripristinare il comportamento precedente. Abbiamo lavorato per garantire la compatibilità con le versioni precedenti per le modifiche apportate consentendo di ripristinare il vecchio jobID o usando un flag. Tuttavia, come indicato in precedenza, non è consigliabile implementare il comportamento precedente. È consigliabile usare il nuovo comportamento per tutte le nuove implementazioni e aggiornare le implementazioni esistenti.

| **Problema di conformità con SCIM 2.0** |  **Fissa?** | **Data di risoluzione**  |  **Compatibilità con le versioni precedenti** |
|---|---|---|
| In Azure AD, "/ scim" deve trovarsi nella radice dell’URL endpoint SCIM dell’applicazione  | Sì  |  18 dicembre 2018 | effettuare il downgrade a customappSSO |
| Per gli attributi di estensione, si utilizza la notazione punto "." prima dei nomi di attributo anziché i due punti ":" |  Sì  | 18 dicembre 2018  | effettuare il downgrade a customappSSO |
| Le richieste di patch per gli attributi multivalore contengono una sintassi del filtro del percorso non valida | Sì  |  18 dicembre 2018  | effettuare il downgrade a customappSSO |
| Le richieste di creazione dei gruppi contengono un URI di schema non valido | Sì  |  18 dicembre 2018  |  effettuare il downgrade a customappSSO |
| Aggiornare il comportamento della PATCH per garantire la conformità | No | TBD| USA flag di anteprima |

## <a name="flags-to-alter-the-scim-behavior"></a>Flag per modificare il comportamento di SCIM
Usare i flag seguenti nell'URL del tenant dell'applicazione per modificare il comportamento predefinito del client SCIM.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM contrassegna il comportamento successivo.":::

* Usare l'URL seguente per aggiornare il comportamento della PATCH e garantire la conformità di SCIM. Questo comportamento è attualmente disponibile solo quando si usa il flag, ma diventerà il comportamento predefinito nei prossimi mesi.
  * **URL (conforme a SCIM):** AzureAdScimPatch062020
  * **Riferimenti RFC SCIM:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Comportamento**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **URL di downgrade:** Quando il nuovo comportamento conforme a SCIM diventa l'impostazione predefinita nell'applicazione non della raccolta, è possibile usare l'URL seguente per eseguire il rollback al comportamento precedente, non conforme a SCIM: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Aggiornamento dal processo customappsso precedente al processo SCIM
Seguendo la procedura riportata di seguito, il processo customappsso esistente viene eliminato e viene creato un nuovo processo SCIM. 
 
1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella sezione **Azure Active Directory > Applicazioni aziendali** del portale di Azure, individuare e selezionare la propria applicazione SCIM.
3. Nella sezione **Proprietà** della propria app SCIM, copiare l’**ID oggetto**.
4. In una nuova finestra del browser Web, accedere a https://developer.microsoft.com/graph/graph-explorer e accedere come amministratore del tenant di Azure AD in cui deve essere aggiunta l'app.
5. In Graph explorer, eseguire il comando seguente per individuare l'ID del processo di provisioning. Sostituire "[id-oggetto]" con l’ID principale del servizio (ID oggetto) copiato nel terzo passaggio.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Ottenere i processi](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Ottenere i processi") 


6. Nei risultati, copiare la stringa "ID" completa che inizia con "customappsso" o "scim".
7. Eseguire il comando seguente per recuperare la configurazione di mapping degli attributi per eseguirne un backup. Usare lo stesso [id-oggetto] di prima e sostituire [id-processo] con l'ID del processo di provisioning copiato nell'ultimo passaggio.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Ottenere lo schema](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Ottenere lo schema") 

8. Copiare l'output JSON dall'ultimo passaggio e salvarlo in un file di testo. Il file JSON contiene tutti i mapping di attributi personalizzati aggiunti all'app precedente e deve essere approssimativamente costituito da un numero di migliaia di righe di JSON.
9. Eseguire il comando seguente per eliminare il processo di provisioning:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Eseguire il comando seguente per creare un nuovo processo di provisioning con le correzioni più recenti di servizio.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Nei risultati dell’ultimo passaggio, copiare la stringa "ID" completa che inizia con "scim". Facoltativamente, riapplicare i vecchi mapping degli attributi eseguendo il comando seguente, sostituendo [New-job-ID] con il nuovo ID processo copiato e immettendo l'output JSON dal passaggio #7 come corpo della richiesta.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Tornare alla prima finestra del browser Web e selezionare la scheda **Provisioning** dell'applicazione.
13. Verificare la configurazione, quindi avviare il processo di provisioning. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Downgrade dal processo SCIM al processo customappsso (scelta non consigliata)
 È possibile eseguire il downgrade al comportamento precedente, ma non è consigliabile perché customappsso non trae vantaggio da alcuni degli aggiornamenti che vengono eseguiti e potrebbe non essere supportato per sempre. 

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Nella sezione **Azure Active Directory > Applicazioni aziendali > Crea applicazione** del portale di Azure, creare una nuova applicazione **Non nella raccolta**.
3. Nella sezione **Proprietà** della nuova app personalizzata, copiare l’**ID oggetto**.
4. In una nuova finestra del browser Web, accedere a https://developer.microsoft.com/graph/graph-explorer e accedere come amministratore del tenant di Azure AD in cui deve essere aggiunta l'app.
5. In Graph explorer, eseguire il comando seguente per inizializzare la configurazione del provisioning dell'app.
   Sostituire "[id-oggetto]" con l’ID principale del servizio (ID oggetto) copiato nel terzo passaggio.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Tornare alla prima finestra del browser Web e selezionare la scheda **Provisioning** dell'applicazione.
7. Completare la configurazione del provisioning utenti con la normale procedura.


## <a name="next-steps"></a>Passaggi successivi
[Informazioni sul provisioning e il deprovisioning utenti in applicazioni SaaS](user-provisioning.md)
