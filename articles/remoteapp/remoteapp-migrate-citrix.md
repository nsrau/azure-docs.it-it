---

title: Eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials | Microsoft Docs
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
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials

Esistono alcuni prerequisiti che i clienti di Azure RemoteApp che vogliono eseguire la migrazione a Citrix XenApp Essentials devono tenere presenti. Innanzitutto leggere la [guida tecnica dettagliata alla distribuzione per Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) e la relativa [libreria tecnica online](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html). 

## <a name="prerequisite-steps-for-migration"></a>Passaggi preliminari per la migrazione

1. Creare una nuova rete virtuale o determinare la rete virtuale di Azure in Azure Resource Manager in cui si distribuirà Citrix XenApp Essentials. Azure RemoteApp usa il portale di Azure classico, mentre Citrix XenApp Essentials supporta solo Azure Resource Manager.  
2. Assicurarsi che la rete virtuale selezionata abbia accesso di rete al controller di dominio, perché Citrix supporta solo distribuzioni ibride. Se si usa una distribuzione cloud di Azure RemoteApp, verificare che la rete virtuale abbia accesso di rete a un controller di dominio di Active Directory. È anche possibile usare Azure Active Directory Domain Services, ovvero Azure AD DS. 
3. Verificare che il DNS sia configurato correttamente per la rete virtuale, in modo che l'aggiunta a un dominio abbia esito positivo al primo tentativo. È possibile creare una macchina virtuale nella rete virtuale selezionata ed eseguire un'aggiunta a un dominio manuale per verificare che la DNS e l'aggiunta a un dominio funzionino come previsto. In questo modo la distribuzione di Citrix XenApp Essentials verrà eseguita correttamente al primo tentativo. 
4. Se necessario, creare un peering di rete virtuale tra rete virtuale classica di Azure in uso con Azure RemoteApp e la rete virtuale di Azure Resource Manager. Questo processo di peering funziona se le due reti si trovano nella stessa area. In caso contrario, usare la VPN da sito a sito per connettere le reti virtuali alla rete. 
5. Se necessario, vedere [Come eseguire la migrazione di dati in entrata e in uscita da Azure RemoteApp](remoteapp-migrate.md). 
6. Aggiornare l'immagine di Azure RemoteApp esistente per includere il componente VDA di Citrix. Per istruzioni, vedere la documentazione di Citrix. 
7. Passare ad Azure Marketplace e iniziare la distribuzione di Citrix XenApp Essentials.

## <a name="other-considerations"></a>Altre considerazioni

Quando si esegue la migrazione è necessario tenere presenti le considerazioni aggiuntive seguenti:
- Citrix XenApp Essentials supporta solo distribuzioni ibride. In altre parole richiede l'accesso di rete a un controller di dominio per eseguire l'aggiunta a un dominio. Se si usa una distribuzione cloud di Azure RemoteApp, usare AAD-DS o assicurarsi che la rete virtuale abbia accesso ad Active Directory per l'aggiunta a un dominio. 
- Per informazioni su come spostare i dati utente in Citrix XenApp Essentials, vedere [Come eseguire la migrazione da Azure RemoteApp a Citrix XenApp Essentials](remoteapp-migrate.md). 
- Citrix XenApp Essentials supporta solo account Active Directory. Non supporta gli account Microsoft, ad esempio outlook.com, msn.com o hotmail.com. 

## <a name="citrix-xenapp-essentials-billing"></a>Fatturazione di Citrix XenApp Essentials

Per informazioni dettagliate sui prezzi, vedere le [domande frequenti](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) e l'[articolo sulla panoramica di Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Nella fatturazione per Citrix XenApp Essentials vengono incluse tre voci:

- L'addebito per il servizio Citrix è di $ 12 per utente al mese. Come per tutti gli altri acquisti di Azure Marketplace, questa voce viene fatturata con il metodo di pagamento associato alla sottoscrizione di Azure. I clienti con contratto Enterprise non possono usare i crediti monetari di Azure. 
- Licenze di accesso al client Remote Data Services (RDS). Attualmente, è possibile acquistare il pacchetto di accesso remoto offerto con il pagamento di $ 6,25 per Remote Access Fee. I clienti EA possono usare i crediti monetari di Azure per pagare questo servizio. Per usare le licenze CAL di Remote Data Sevice, inviare un messaggio di posta elettronica all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com) in modo che vengano inserite nella fatturazione. 
- Calcolo e archiviazione di Azure. Si tratta del costo di Archiviazione di Azure e del consumo del calcolo per le macchine virtuali usate. Tenere presenti i prezzi quando si selezionano le dimensioni della macchina virtuale e la densità di utenti. I clienti EA possono usare i crediti monetari di Azure per pagare questo servizio.

In caso di altre domande, è possibile:
- Scrivere un'e-mail all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
- [Contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per problemi relativi ai passaggi preliminari da 1 a 5, [aprire un caso di supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Per problemi relativi ai passaggi 6 e 7, contattare Citrix aprendo un ticket di supporto nel portale di gestione di Citrix. 

