---
title: Integrazione di Azure Data Center di Stack - sicurezza
description: Informazioni su come integrare la protezione di Azure Stack con la sicurezza del Data Center
services: azure-stack
author: jeffgilb
ms.service: azure-stack
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 16d97765c9340555bfc0db22975a43227cc6b517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Integrazione di Azure Data Center di Stack - sicurezza
Stack di Azure è stato progettato e compilati con particolare attenzione alla sicurezza. Stack di Azure è un sistema bloccato, pertanto non è supportata l'installazione dell'agente di protezione software.

In questo articolo consente di integrare le funzionalità di sicurezza di Azure Stack con le soluzioni di protezione già distribuite nel Data Center.

## <a name="security-logs"></a>Registri di protezione

Stack Azure raccoglie del sistema operativo e gli eventi di sicurezza per i ruoli di infrastruttura e i nodi di unità di scala ogni due minuti. I log vengono archiviati nei contenitori blob account di archiviazione.

Non vi è un account di archiviazione per ogni ruolo di infrastruttura e un account di archiviazione generale per tutti gli eventi di sistema operativo tipico.

Il provider di risorse di integrità può essere chiamato tramite il protocollo REST per recuperare l'URL del contenitore blob. Le soluzioni di protezione di terze parti possono utilizzare gli account di archiviazione e l'API per recuperare gli eventi per l'elaborazione.

### <a name="use-azure-storage-explorer-to-view-events"></a>Usare Azure Storage Explorer per visualizzare gli eventi

È possibile recuperare gli eventi raccolti dallo Stack di Azure utilizzando uno strumento denominato Azure Storage Explorer. È possibile scaricare Azure Storage Explorer da [http://storageexplorer.com](http://storageexplorer.com).

La procedura seguente è riportato un esempio che è possibile utilizzare per configurare Azure Storage Explorer per lo Stack di Azure:

1. Accedere al portale di amministrazione di Azure Stack come un operatore.
2. Sfoglia **gli account di archiviazione** e cercare **frphealthaccount**. Il **frphealthaccount** account è l'account di archiviazione generale utilizzato per archiviare tutti gli eventi di sistema operativo.

   ![Account di archiviazione](media/azure-stack-integrate-security/storage-accounts.png)

3. Selezionare **frphealthaccount**, quindi fare clic su **chiavi di accesso**.

   ![Chiavi di accesso](media/azure-stack-integrate-security/access-keys.png)

4. Copiare la chiave di accesso agli Appunti.
5. Aprire Azure Storage Explorer.
6. Nel **modifica** dal menu **dello Stack di Azure di destinazione**.
7. Selezionare **Aggiungi Account**, quindi selezionare **utilizzare un nome account di archiviazione e una chiave**.

   ![La connessione di archiviazione](media/azure-stack-integrate-security/connect-storage.png)

8. Fare clic su **Avanti**.
9. Nel **collega archiviazione esterna** pagina:

   a. Digitare il nome dell'account **frphealthaccount**.

   b. Incollare la chiave di accesso di account di archiviazione.

   c. In **dominio gli endpoint di archiviazione**selezionare **altri**e specificare l'endpoint di archiviazione **[Region]. [ DomainName]**.

   d. Selezionare il **Usa HTTP** casella di controllo.

   ![Collega archiviazione esterna](media/azure-stack-integrate-security/attach-storage.png)

10. Fare clic su **Avanti**, esaminare il riepilogo, e **fine** la procedura guidata.
11. È possibile esplorare i contenitori blob singolo e scaricare gli eventi.

   ![Individuare i BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Utilizzare linguaggi di programmazione per eventi di accesso

È possibile utilizzare diversi linguaggi di programmazione per accedere a un account di archiviazione. Per scegliere un esempio in cui la lingua corrispondente, utilizzare la seguente documentazione:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Controllo dell'accesso ai dispositivi

Tutti i dispositivi fisici nello Stack di Azure supportano l'utilizzo di TACAS o RADIUS. Ciò include l'accesso a baseboard management controller (BMC) e commutatori di rete.

Soluzioni Stack Azure non vengono forniti con RADIUS o TACACS incorporata. Tuttavia, le soluzioni sono state convalidate per supportare l'utilizzo di RADIUS o TACACS soluzioni esistenti disponibili sul mercato.

MSCHAPv2 RADIUS, è stata convalidata. Rappresenta l'implementazione più sicura tramite RADIUS.
Rivolgersi al fornitore dell'hardware OEM per abilitare TACAS o RADIUS nei dispositivi inclusi con la soluzione di Stack di Azure.

## <a name="syslog"></a>syslog

Tutti i dispositivi fisici nello Stack di Azure è possono inviare i messaggi Syslog. Soluzioni di Azure Stack non viene fornito un server Syslog. Tuttavia, le soluzioni sono state convalidate per supportare l'invio di messaggi alle soluzioni esistenti Syslog disponibili sul mercato.

L'indirizzo di destinazione Syslog è un parametro facoltativo raccolto per la distribuzione, ma è anche possibile aggiungere post-distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Criteri di manutenzione](azure-stack-servicing-policy.md)
