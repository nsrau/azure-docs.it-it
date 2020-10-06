---
title: Avvio rapido per la configurazione e la distribuzione di Azure Stack Edge GPU | Microsoft Docs
description: Introduzione alla distribuzione di Azure Stack Edge GPU dopo la ricezione del dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 09/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to quickly deploy Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 200e7f191cb19588fa79ba685c91a75dabd0156a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348206"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Avvio rapido: Introduzione ad Azure Stack Edge Pro con GPU 

Questo argomento di avvio rapido illustra in modo dettagliato i prerequisiti e i passaggi necessari per distribuire il dispositivo Azure Stack Edge Pro GPU. I passaggi dell'avvio rapido vengono eseguiti nel portale di Azure e nell'interfaccia utente Web locale del dispositivo. 

Per completare tutta la procedura sono necessarie circa 1,5 ore. Per istruzioni dettagliate, vedere [Esercitazione: Preparare la distribuzione di Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist). 


## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire la distribuzione, verificare che siano soddisfatti i prerequisiti seguenti:

1. Il dispositivo Azure Stack Edge Pro GPU è stato consegnato, [disimballato](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) e [montato su rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device). 
1. Configurare la rete in modo che il dispositivo possa raggiungere le [porte e i modelli URL elencati ](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Si ha accesso alla sottoscrizione di Azure come proprietario o collaboratore.
1. Nel portale di Azure passare a **Home > Sottoscrizioni > Sottoscrizione personale > Provider di risorse**. Cercare `Microsoft.DataBoxEdge` e registrare il provider di risorse. Ripetere i passaggi per registrare `Microsoft.Devices` se si creerà una risorsa hub IoT per la distribuzione dei carichi di lavoro di calcolo.
1. Assicurarsi di avere almeno 2 indirizzi IP statici contigui disponibili per i nodi Kubernetes e almeno 1 indirizzo IP statico per il servizio IoT Edge. Per ogni modulo o servizio esterno da distribuire è necessario un IP aggiuntivo.
1. Vedere l'[elenco di controllo per la distribuzione ](azure-stack-edge-gpu-deploy-checklist.md) per informazioni su tutto ciò che è necessario per la configurazione del dispositivo. 


## <a name="deployment-steps"></a>Passaggi di distribuzione

1. **Installare**: connettere la PORTA 1 a un computer client tramite un cavo incrociato o una scheda Ethernet USB. Connettere almeno un'altra porta del dispositivo per i dati, preferibilmente 25 GbE (dalla PORTA 3 alla PORTA 6) a Internet tramite un commutatore (almeno 1 GbE) e cavi in rame SFP+. Connettere i cavi di alimentazione forniti alle unità di alimentazione e a uscite di distribuzione dell'alimentazione separate. Premere il pulsante di alimentazione sul pannello anteriore per accendere il dispositivo.  

    Vedere la [matrice di interoperabilità per la serie Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e i [prodotti compatibili con la scheda di rete Mellanox a due porte 25G ConnectX-4 canali](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) per informazioni su cavi di rete e commutatori compatibili.

    Di seguito è illustrata la configurazione di cablaggio minima necessaria per la distribuzione del dispositivo:  ![Pannello posteriore di un dispositivo cablato](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Connessione**: Configurare le impostazioni IPv4 nella scheda Ethernet nel computer con un indirizzo IP statico **192.168.100.5** e una subnet **255.255.255.0**. Aprire il browser e connettersi all'interfaccia utente Web locale di un dispositivo in https://192.168.100.10. L'operazione potrebbe richiedere alcuni minuti. Passare alla pagina Web quando viene visualizzato l'avviso relativo al certificato di sicurezza.

3. **Accedere**: accedere al dispositivo con la password predefinita *Password1*. Modificare la password di amministratore del dispositivo. La password deve contenere da 8 a 16 caratteri e tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.

4. **Configurare la rete**: se nella rete è presente un server DHCP, accettare la configurazione DHCP predefinita per la porta dati connessa. In caso contrario, specificare un indirizzo IP, un server DNS e un gateway predefinito. Vedere altre informazioni sulle [impostazioni di rete](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network).

5. **Configurare la rete di calcolo**: creare un commutatore virtuale abilitando una porta nel dispositivo. Immettere 2 indirizzi IP statici contigui disponibili per i nodi Kubernetes nella stessa rete in cui è stato creato il commutatore. Fornire almeno 1 indirizzo IP statico per consentire al servizio hub di IoT Edge di accedere ai moduli di calcolo e 1 indirizzo IP statico per ogni servizio o contenitore aggiuntivo a cui si vuole accedere dall'esterno del cluster Kubernetes. 

    Kubernetes è necessario per la distribuzione di tutti i carichi di lavoro in contenitori. Vedere altre informazioni sulle [impostazioni della rete di calcolo](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network).

6. **Configurare il proxy Web**: se si usa il proxy Web nell'ambiente, immettere l'IP del server proxy Web in `http://<web-proxy-server-FQDN>:<port-id>`. Impostare l'autenticazione su **Nessuna**. Vedere altre informazioni sulle [impostazioni del proxy Web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Configurare il dispositivo**: immettere il nome di dispositivo e il dominio DNS o accettare le impostazioni predefinite. 

8. **Configurare il server di aggiornamento**: accettare il server Microsoft Update predefinito o specificare un server Windows Server Update Services (WSUS) e il percorso del server. 

9. **Configurare le impostazioni di ora**: Accettare le impostazioni di ora predefinite oppure impostare il fuso orario, il server NTP primario e il server NTP secondario nella rete locale o come server pubblici.

10. **Configurare i certificati**: se sono state apportate modifiche al nome del dispositivo e/o al dominio DNS, è necessario generare o aggiungere i certificati per attivare il dispositivo. 

    - Per testare i carichi di lavoro non di produzione, usare l'[opzione Genera certificati](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - Se si usano certificati personalizzati, incluse le catene di firma, [aggiungere i certificati](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) nel formato appropriato. Assicurarsi di caricare prima la catena di firma. Vedere [Creare i certificati](azure-stack-edge-j-series-create-certificates-tool.md) e [Caricare i certificati tramite l'interfaccia utente locale](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Activate**: per ottenere la chiave di attivazione 

    1. Nel portale di Azure passare a **risorsa Azure Stack Edge > Panoramica > Configurazione dispositivo > Attiva > Genera chiave**. Copiare la chiave. 
    1. Nell'interfaccia utente Web locale passare a **Per iniziare > Attiva** e specificare la chiave di attivazione. Dopo l'applicazione della chiave, per l'attivazione del dispositivo sono necessari alcuni minuti. Scaricare il file `<device-serial-number>`.json quando viene richiesto per archiviare in modo sicuro le chiavi del dispositivo necessarie per un ripristino futuro. 

12. **Configurare le risorse di calcolo**: nel portale di Azure passare a **Panoramica > Dispositivo**. Verificare che il dispositivo sia **Online**. Nel riquadro sinistro passare a **Calcolo Edge > Per iniziare > Configura calcolo Edge > Calcolo**. Fornire un servizio hub IoT nuovo o esistente e attendere circa 20 minuti per la configurazione delle risorse di calcolo. Per altre informazioni, vedere [Esercitazione: Configurare il calcolo nel dispositivo Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-configure-compute.md)

È possibile distribuire i carichi di lavoro di calcolo nel dispositivo [tramite IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md), [tramite `kubectl`](azure-stack-edge-gpu-create-kubernetes-cluster.md) o [tramite Kubernetes abilitato per Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Se si verificano problemi durante la configurazione, vedere [Risolvere i problemi relativi ai dispositivi](), [i problemi relativi agli ordini](azure-stack-edge-gpu-troubleshoot.md), [i problemi relativi ai certificati](azure-stack-edge-j-series-certificate-troubleshooting.md) o i problemi di Kubernetes. 

## <a name="next-steps"></a>Passaggi successivi

[Installare Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-install.md)



