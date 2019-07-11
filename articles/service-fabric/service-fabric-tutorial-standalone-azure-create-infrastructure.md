---
title: "Esercitazione: Creazione dell'infrastruttura per un cluster di Service Fabric in macchine virtuali di Azure - Azure Service Fabric | Microsoft Docs"
description: In questa esercitazione si apprende come configurare l'infrastruttura delle macchine virtuali di Azure per l'esecuzione di un cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276189"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Esercitazione: Creare un'infrastruttura di macchine virtuali di Azure per ospitare un cluster di Service Fabric

I cluster autonomi di Service Fabric offrono la possibilità di scegliere il proprio ambiente e creare un cluster come parte dell'approccio "qualsiasi sistema operativo, qualsiasi cloud" adottato da Service Fabric. In questa serie di esercitazioni viene creato un cluster autonomo ospitato in macchine virtuali di Azure e viene installata un'applicazione al suo interno.

Questa è la prima di una serie di esercitazioni. In questo articolo vengono generate le risorse delle macchine virtuali di Azure necessarie per ospitare il cluster autonomo di Service Fabric. Nei prossimi articoli si installerà la suite autonoma di Service Fabric, si installerà un'applicazione di esempio nel cluster e, infine, si pulirà il cluster.

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un set di istanze di macchine virtuali di Azure
> * Modificare il gruppo di sicurezza
> * Accedere a una delle istanze
> * Preparare l'istanza per Service Fabric

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessaria una sottoscrizione di Azure.  Se non si ha ancora un account, accedere al [portale di Azure](https://portal.azure.com) per crearne uno.

## <a name="create-azure-virtual-machine-instances"></a>Creare istanze di macchine virtuali di Azure

1. Accedere al portale di Azure e selezionare **Macchine virtuali** (non Macchine virtuali (versione classica)).

   ![Macchina virtuale nel portale di Azure][az-console]

2. Selezionare il pulsante **Aggiungi**, che aprirà il modulo **Crea macchina virtuale**.

3. Nella scheda **Generale** assicurarsi di scegliere la sottoscrizione e il gruppo di risorse voluto. È consigliabile scegliere un nuovo gruppo di risorse.

4. Cambiare il tipo **Immagine** in **Windows Server 2016 Datacenter**. 
 
5. Cambiare le **Dimensioni** dell'istanza in **DS2 Standard v2**. Impostare **Nome utente** e **Password** dell'amministratore, prendendone nota.

6. Lasciare bloccate le **Regole porta in ingresso** per il momento. Verranno configurate nella prossima sezione.

7. Nella scheda **Rete** creare una nuova **Rete virtuale** e prendere nota del nome di questa.

8. Impostare quindi il **Gruppo di sicurezza di rete della scheda di interfaccia di rete** su **Avanzato**. Creare un nuovo gruppo di sicurezza, prendendo nota del nome di questo, e creare le regole seguenti per consentire il traffico TCP da qualsiasi origine:

   ![sf-inbound][sf-inbound]

   * Porta `3389`, per RDP e ICMP (connettività di base).
   * Porte `19000-19003`, per Service Fabric.
   * Porte `19080-19081`, per Service Fabric.
   * Porta `8080`, per le richieste del Web browser.

   > [!TIP]
   > Per connettere le macchine virtuali insieme in Service Fabric, le macchine virtuali che ospitano l'infrastruttura devono avere le stesse credenziali.  Esistono due modi comuni per ottenere credenziali coerenti: unirle tutte allo stesso dominio o impostare la stessa password di amministratore in ogni macchina virtuale. Azure, fortunatamente, consente a tutte le macchine virtuali nella stessa **rete virtuale** di connettersi con facilità. È quindi necessario assicurarsi che tutte le istanze si trovino all'interno della stessa rete.

9. Aggiungere un'altra regola. Impostare **Service Tag** come origine e **VirtualNetwork** come tag del servizio di origine. Per la comunicazione all'interno del cluster, Service Fabric richiede che le porte seguenti siano aperte: 135, 137-139, 445, 20001-20031, 20606-20861.

   ![vnet-inbound][vnet-inbound]

10. Per le opzioni rimanenti è accettabile lo stato predefinito di ognuna. Rivedere le opzioni, se si vuole, e quindi avviare la macchina virtuale.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Creazione di più istanze per il cluster di Service Fabric

Avviare altre due **macchine virtuali**, assicurandosi di mantenere le stesse impostazioni descritte nella sezione precedente. In particolare, mantenere lo stesso nome utente e la stessa password dell'amministratore. **Rete virtuale** e **Gruppo di sicurezza di rete della scheda di interfaccia di rete** non devono essere creati di nuovo. Selezionare quelli già creati dal menu a discesa. La distribuzione di ogni istanza può richiedere qualche minuto.

## <a name="connect-to-your-instances"></a>Connettersi alle istanze

1. Selezionare una delle istanze dalla sezione **Macchina virtuale**.

2. Nella scheda **Panoramica** prendere nota dell'indirizzo IP *privato* e quindi fare clic su **Connetti**.

3. Nella scheda **RDP** si noti che si stanno usando l'indirizzo IP pubblico e la porta 3389, che è stata appositamente aperta in precedenza. Scaricare il file RDP.
 
4. Aprire il file RDP e, quando richiesto, immettere il nome utente e la password specificati durante la configurazione della macchina virtuale.

5. Dopo la connessione a un'istanza è necessario verificare che Registro di sistema remoto sia in esecuzione, abilitare SMB e aprire le porte necessarie per SMB e Registro di sistema remoto.

   Per abilitare SMB usare questo comando PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Questo è il comando di PowerShell per aprire le porte nel firewall:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Ripetere questo processo per le altre istanze, prendendo di nuovo nota degli indirizzi IP privati.

## <a name="verify-your-settings"></a>Verificare le impostazioni

1. Per convalidare la connettività di base, connettersi a una delle VM tramite RDP.

2. Aprire il **prompt dei comandi** all'interno della macchina virtuale scelta e quindi usare il comando ping per connettersi da una macchina virtuale a un'altra, sostituendo l'indirizzo IP sottostante con uno degli indirizzi IP privati annotati in precedenza, ma non con l'indirizzo IP della macchina virtuale a cui si è già connessi.

   ```
   ping 172.31.20.163
   ```

   Se l'output avrà un aspetto simile a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` ripetuto quattro volte, significa che la connessione tra le istanze è funzionante.

3. A questo punto convalidare che la condivisione SMB funzioni con il comando seguente:

   ```
   net use * \\172.31.20.163\c$
   ```

   L'output restituito dovrebbe essere `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Le istanze sono state preparate correttamente per Service Fabric.

## <a name="next-steps"></a>Passaggi successivi

Nella prima parte della serie è stato descritto come avviare tre istanze di macchine virtuali di Azure e come configurarle per l'installazione di Service Fabric:

> [!div class="checklist"]
> * Creare un set di istanze di macchine virtuali di Azure
> * Modificare il gruppo di sicurezza
> * Accedere a una delle istanze
> * Preparare l'istanza per Service Fabric

Passare alla seconda parte della serie per configurare Service Fabric nel cluster.

> [!div class="nextstepaction"]
> [Installare Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
