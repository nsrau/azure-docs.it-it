---
title: Integrazione di Data Center Azure Stack - sicurezza
description: Informazioni su come integrare la protezione di Azure Stack con la sicurezza dei Data Center
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442476"
---
# <a name="azure-stack-datacenter-integration---security"></a>Integrazione di Data Center Azure Stack - sicurezza
Azure Stack è stato progettato e compilato con particolare attenzione alla sicurezza. Azure Stack è un sistema bloccato, in modo che l'installazione dell'agente di protezione software non è supportato.

Questo articolo consente di integrare le funzionalità di sicurezza di Azure Stack con le soluzioni di sicurezza già distribuite nel Data Center.

## <a name="security-logs"></a>Log di sicurezza

Azure Stack raccoglie del sistema operativo e gli eventi di sicurezza per i ruoli di infrastruttura e i nodi di unità di scala ogni due minuti. I log vengono archiviati in contenitori blob dell'account di archiviazione.

C'è un account di archiviazione per ogni ruolo di infrastruttura e un account di archiviazione generale per tutti gli eventi di sistema operativo tipico.

Il provider di risorse di integrità può essere chiamato tramite il protocollo REST per recuperare l'URL del contenitore blob. Soluzioni di sicurezza di terze parti è possono usare l'account di archiviazione e API per recuperare gli eventi per l'elaborazione.

### <a name="use-azure-storage-explorer-to-view-events"></a>Usare Azure Storage Explorer per visualizzare gli eventi

È possibile recuperare gli eventi raccolti da Azure Stack tramite uno strumento denominato Azure Storage Explorer. È possibile scaricare Azure Storage Explorer dal [ http://storageexplorer.com ](http://storageexplorer.com).

La procedura seguente è un esempio che è possibile usare per configurare Azure Storage Explorer per Azure Stack:

1. Accedere al portale di amministrazione di Azure Stack come operatore.
1. Esplorare **gli account di archiviazione** e cercare **frphealthaccount**. Il **frphealthaccount** account è l'account di archiviazione generale consente di archiviare tutti gli eventi di sistema operativo.

   ![Account di archiviazione](media/azure-stack-integrate-security/storage-accounts.png)

1. Selezionare **frphealthaccount**, quindi fare clic su **chiavi di accesso**.

   ![Chiavi di accesso](media/azure-stack-integrate-security/access-keys.png)

1. Copiare la chiave di accesso negli Appunti.
1. Aprire Azure Storage Explorer.
1. Nel **Edit** dal menu **Target Azure Stack**.
1. Selezionare **Add Account**, quindi selezionare **usare un nome account di archiviazione e la chiave**.

   ![Connettersi ad archiviazione](media/azure-stack-integrate-security/connect-storage.png)

1. Fare clic su **Avanti**.
1. Nel **associa archiviazione esterna** pagina:

   a. Digitare il nome dell'account **frphealthaccount**.

   b. Incollare la chiave di accesso di account di archiviazione.

   c. Sotto **dominio endpoint archiviazione**, selezionare **altri**e specificare l'endpoint archiviazione **[Region]. [ DomainName]**.

   d. Selezionare il **Usa HTTP** casella di controllo.

   ![Associa archiviazione esterna](media/azure-stack-integrate-security/attach-storage.png)

1. Fare clic su **successivo**, esaminare il riepilogo, e **fine** la procedura guidata.
1. È ora possibile esplorare i contenitori blob singolo e scaricare gli eventi.

   ![Esplora BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Usare linguaggi di programmazione per accedere agli eventi

È possibile usare diversi linguaggi di programmazione per accedere a un account di archiviazione. Usare la documentazione seguente per selezionare un esempio che corrisponde alla lingua:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Il controllo di accesso dispositivo

Tutti i dispositivi fisici in Azure Stack supportano l'uso di TACACS o RADIUS. Ciò include l'accesso a baseboard management controller (BMC) e commutatori di rete.

Soluzioni di Azure Stack non vengono forniti con RADIUS o TACACS incorporato. Tuttavia, le soluzioni sono state convalidate per supportare l'uso di RADIUS o TACACS soluzioni esistenti disponibili sul mercato.

MSCHAPv2 RADIUS, è stata convalidata. Rappresenta l'implementazione più sicura tramite RADIUS.
Rivolgersi al fornitore dell'hardware OEM per abilitare TACAS o RADIUS nei dispositivi inclusi con la soluzione di Azure Stack.

## <a name="syslog"></a>syslog

Tutti i dispositivi fisici in Azure Stack possono inviare messaggi di Syslog. Soluzioni di Azure Stack non viene fornito un server Syslog. Tuttavia, le soluzioni sono state convalidate per supportare l'invio di messaggi alle soluzioni esistenti di Syslog disponibili sul mercato.

L'indirizzo di destinazione Syslog è un parametro facoltativo raccolto per la distribuzione, ma può anche essere aggiunto dopo la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

[Criteri di manutenzione](azure-stack-servicing-policy.md)
