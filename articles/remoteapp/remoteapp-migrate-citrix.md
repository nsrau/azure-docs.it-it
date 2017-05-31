---

title: Come eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials | Microsoft Docs
description: Come eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Come eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials

Esistono alcuni prerequisiti per i clienti di Azure RemoteApp che vogliono eseguire la migrazione a Citrix XenApp Essentials.  È consigliabile vedere prima la [guida tecnica dettagliata alla distribuzione per Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) e la [libreria tecnica online](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html) di Citrix. 

Fortunatamente è possibile riutilizzare la maggior parte degli investimenti già fatti per Azure RemoteApp, ma esistono alcuni prerequisiti generali per la distribuzione di XenApp Essentials.

## <a name="prerequisites"></a>Prerequisiti

1. Creare una nuova rete virtuale o determinare la rete virtuale di Azure in Azure Resource Manager in cui si distribuirà Citrix XenApp Essentials. Azure RemoteApp usa la distribuzione classica di Azure, mentre Citrix XenApp Essentials supporta solo Azure Resource Manager.  
2. Assicurarsi che la rete virtuale selezionata abbia accesso di rete al controller di dominio, perché Citrix supporta solo distribuzioni ibride. Se si usa una distribuzione cloud di Azure RemoteApp, sarà necessario assicurarsi che la rete virtuale abbia accesso di rete al controller di dominio Active Directory e/o è consigliabile usare Azure Active Directory Domain Services (AAD-DS). 
3. Assicurarsi che DNS sia configurato correttamente per la rete virtuale in modo che l'aggiunta a un dominio riesca al primo tentativo. È possibile creare una macchina virtuale nella rete virtuale selezionata ed eseguire un'aggiunta a un dominio manuale per verificare che DNS e l'aggiunta a un dominio funzionino come previsto. In questo modo la distribuzione di Citrix XenApp Essentials verrà eseguita correttamente al primo tentativo. 
4. Se necessario, creare un peering reti virtuali tra una rete virtuale classica di Azure usata con Azure RemoteApp e la rete virtuale di Azure Resource Manager, se si trovano nella stessa area, e/o usare una VPN da sito a sito in caso contrario, per connettere le reti virtuali. 
5. Se necessario, vedere [Come eseguire la migrazione di dati in entrata e in uscita da Azure RemoteApp](remoteapp-migrate.md). 
6. Aggiornare l'immagine di Azure RemoteApp esistente per includere il componente VDA di Citrix. Per istruzioni, vedere la documentazione di Citrix. 
7. Passare ad Azure Marketplace e iniziare la distribuzione di Citrix XenApp Essentials.

Grazie per avere usato Azure RemoteApp. 

## <a name="other-considerations"></a>Altre considerazioni:

- Citrix XenApp Essentials supporta solo distribuzioni ibride. In altre parole richiede l'accesso di rete a un controller di dominio per eseguire l'aggiunta a un dominio. Se si usa una distribuzione cloud di Azure RemoteApp, sarà necessario usare AAD-DS o assicurarsi che la rete virtuale abbia accesso ad Active Directory per l'aggiunta a un dominio. 
- Per informazioni su come spostare i dati utente in CXE, vedere [Come eseguire la migrazione di dati in entrata e in uscita da Azure RemoteApp](remoteapp-migrate.md). 
- Citrix XenApp Essentials supporta solo account Active Directory. Non supporta account Microsoft (@outlook.com, @msn.com, @hotmail.com e così via). 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Informazioni sulla fatturazione per Citrix XenApp Essentials 

Per informazioni dettagliate complete sui prezzi, vedere le [domande frequenti](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) e l'[articolo sulla panoramica di Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Nella fatturazione per Citrix XenApp Essentials vengono incluse le tre voci seguenti:

1. Addebito per il servizio Citrix di $ 12 per utente al mese. Come per tutti gli altri acquisti di Azure Marketplace, questa voce viene fatturata con il metodo di pagamento associato alla sottoscrizione di Azure. I clienti con contratto Enterprise non possono usare i crediti monetari di Azure. 
2. Licenza CAL di Servizi Desktop remoto. È necessario usare la licenza CAL di Servizi Desktop remoto (presto disponibile) personale o pagare un addebito per l'accesso remoto (RAF, Remote Access Fee) che viene incluso nel pagamento per Citrix XenApp Essentials al costo di $ 6,25 (USD). I clienti con contratto Enterprise possono usare i crediti monetari di Azure per questo pagamento. Per usare le licenze CAL di Servizi Desktop remoto esistenti, inviare un messaggio di posta elettronica all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com) in modo che vengano inserite nella fatturazione. 
3. Calcolo e archiviazione di Azure. Si tratta del costo di Archiviazione di Azure e del consumo del calcolo per le VM utilizzate. Tenere presenti i prezzi quando si selezionano le dimensioni della macchina virtuale e la densità di utenti. I clienti con contratto Enterprise possono usare i crediti monetari di Azure per questo pagamento.

Per altre domande:
1. Scrivere un'e-mail all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. [Contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per problemi relativi ai passaggi da 1 a 5 precedenti, [aprire un caso di supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per problemi relativi ai passaggi 6 e 7, contattare Citrix aprendo un ticket di supporto nel portale di gestione di Citrix. 

