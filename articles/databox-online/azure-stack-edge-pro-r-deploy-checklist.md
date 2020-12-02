---
title: Elenco di controllo pre-distribuzione per la distribuzione di Azure Stack dispositivo R Edge Pro | Microsoft Docs
description: Questo articolo descrive le informazioni che è possibile raccogliere prima di distribuire il dispositivo R Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: e5eb008d40b7617272d7d39b71cb4850cb5becfc
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467660"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-r-device"></a>Elenco di controllo della distribuzione per il dispositivo R di Azure Stack Edge Pro  

Questo articolo descrive le informazioni che possono essere raccolte in anticipo rispetto alla distribuzione effettiva del dispositivo R Azure Stack Edge Pro. 

Usare l'elenco di controllo seguente per assicurarsi di avere queste informazioni dopo aver effettuato un ordine per un dispositivo R Azure Stack Edge Pro e prima di aver ricevuto il dispositivo. 

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione 

| Fase                             | Parametro                                                                                                                                                                                                                           | Dettagli                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestione dei dispositivi               | <li>Sottoscrizione di Azure</li><li>Provider di risorse registrati</li><li>Account di archiviazione di Azure</li>|<li>Abilitata per l'accesso Pro/Data Box Gateway, proprietario o collaboratore Azure Stack Edge.</li><li>In portale di Azure passare a **Home > sottoscrizioni > i provider di risorse > sottoscrizione**. Cercare `Microsoft.DataBoxEdge` e registrare. Ripetere l'installazione per `Microsoft.Devices` se si distribuiscono carichi di lavoro Internet.</li><li>Sono necessarie le credenziali di accesso</li> |
| Installazione dispositivi               | Cavi di alimentazione nel pacchetto. <br>Per Microsoft, viene spedito un cavo SVE 18/3 valutato per 125 V e 15 amp con un connettore NEMA da 5 15P a C13 (input in output). | Per ulteriori informazioni, vedere l'elenco dei [cavi di alimentazione supportati per paese](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Almeno 1 cavo di rete RJ-45 da 1 GbE per la porta 1  </li><li> Almeno uno X 25-GbE SFP + cavo di rame per la porta 3, porta 4</li>| Il cliente deve acquistare questi cavi.<br>Per un elenco completo dei cavi di rete supportati, dei commutatori e dei ricetrasmettitori per le schede di rete del dispositivo, vedere la pagina relativa alla [matrice di interoperabilità Cavium FastlinQ serie 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e ai [prodotti compatibili con la scheda di rete Mellanox Dual Port 25](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Prima connessione del dispositivo      | <li>Portatile le cui impostazioni IPv4 possono essere modificate. Questo portatile si connette alla porta 1 tramite uno switch o un adattatore da USB a Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Accesso al dispositivo                       | Password amministratore del dispositivo, da 8 a 16 caratteri e contiene tre dei caratteri seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri speciali.                                            | La password predefinita è *Password1* , che scade al primo accesso.                                                     |
| Impostazioni di rete                  | Il dispositivo è dotato di porte di rete da 2 x 1 GbE, 4 x 25 GbE. <li>La porta 1 viene utilizzata per configurare solo le impostazioni di gestione. Una o più porte dati possono essere connesse e configurate. </li><li> Almeno un'interfaccia di rete dati da una porta 2 alla 6 deve essere connessa a Internet (con connettività ad Azure).</li><li> Sono supportate la configurazione DHCP e IPv4 statica. | Per la configurazione IPv4 statica sono necessari IP, server DNS e gateway predefinito.   |
| Impostazioni di rete di calcolo     | <li>Sono necessari 2 IP disponibili, statici, contigui per i nodi Kubernetes e 1 IP statico per il servizio IoT Edge.</li><li>Richiedere un indirizzo IP aggiuntivo per ogni servizio o modulo aggiuntivo che verrà distribuito.</li>| È supportata solo la configurazione IPv4 statica.|
| Opzionale Impostazioni proxy Web     | <li>IP/FQDN server proxy Web, porta </li><li>Nome utente proxy Web, password</li> | Il proxy Web non è supportato con la configurazione di calcolo. |
| Impostazioni del firewall e della porta        | Se si usa il firewall, assicurarsi che siano consentiti i [modelli e le porte degli URL elencati](azure-stack-edge-system-requirements.md#networking-port-requirements) per gli IP del dispositivo. |  |
| Consigliabile Impostazioni temporali       | Configurare il fuso orario, il server NTP primario, il server NTP secondario. | Configurare il server NTP primario e secondario nella rete locale.<br>Se il server locale non è disponibile, è possibile configurare i server NTP pubblici.                                                    |
| Opzionale Aggiornare le impostazioni del server | <li>Richiedi indirizzo IP del server di aggiornamento nella rete locale, percorso del server WSUS. </li> | Per impostazione predefinita, viene usato il server pubblico di Windows Update.|
| Impostazioni del dispositivo | <li>Nome di dominio completo (FQDN) del dispositivo </li><li>Dominio DNS</li> | |
| Opzionale Certificati  | Se si usano certificati personalizzati, incluse le catene di firma, [aggiungere i certificati](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#bring-your-own-certificates) nel formato appropriato.| Configurare i certificati solo se si modifica il nome del dispositivo e/o il dominio DNS. |
| Connessione  | <!--Need VPN certificate, VPN gateway, firewall setup in Azure,  passphrase and region info VPN scripts. -->   | |
| Crittografia dei dati inattivi  | È consigliabile usare la chiave di crittografia generata automaticamente.   |Se si usa una chiave personalizzata, è necessario disporre di una chiave con codifica base 64 lunga 32 caratteri.  |
| Activation  | Richiedere la chiave di attivazione dalla risorsa Pro/Data Box Gateway di Azure Stack Edge.    | Una volta generato, la chiave scade tra 3 giorni. |

<!--
| (Optional) MAC Address            | If MAC address needs to be whitelisted, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Passaggi successivi

Preparare la distribuzione del [dispositivo Azure stack Edge Pro](azure-stack-edge-pro-r-deploy-prep.md).
