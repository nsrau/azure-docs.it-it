---
title: Elenco di controllo pre-distribuzione per la distribuzione di Azure Stack dispositivo GPU perimetrale | Microsoft Docs
description: Questo articolo descrive le informazioni che è possibile raccogliere prima di distribuire il dispositivo GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 9d7b3388b7e3a31c23b34b21017f012d40e9f849
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268143"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-gpu-device"></a>Elenco di controllo della distribuzione per il dispositivo GPU Azure Stack Edge  

Questo articolo descrive le informazioni che possono essere raccolte in anticipo rispetto alla distribuzione effettiva del dispositivo Azure Stack Edge. 

Usare l'elenco di controllo seguente per assicurarsi di avere queste informazioni dopo aver effettuato un ordine per un dispositivo Azure Stack Edge e prima di aver ricevuto il dispositivo. 

## <a name="deployment-checklist"></a>Elenco di controllo distribuzione 

| Fase                             | Parametro                                                                                                                                                                                                                           | Dettagli                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gestione dei dispositivi               | <li>Sottoscrizione di Microsoft Azure</li><li>API Graph di Azure Active Directory</li><li>Account Archiviazione di Microsoft Azure</li>|<li>Abilitato per Azure Stack Edge/Data Box Gateway, autorizzazioni di collaboratore</li><li>Verificare l'accesso dell'amministratore o dell'utente</li><li>Sono necessarie le credenziali di accesso</li> |
| Installazione dispositivi               | Cavi di alimentazione nel pacchetto. <br>Per Microsoft, viene spedito un cavo SVE 18/3 valutato per 125 V e 15 amp con un connettore NEMA da 5 15P a C13 (input in output).                                                                                                                                                                                                          | Fornito con il dispositivo.<br>Per ulteriori informazioni, vedere l'elenco dei [cavi di alimentazione supportati per paese](azure-stack-edge-technical-specifications-power-cords-regional.md)                                                                                        |
|                                   | <li>Almeno 1 cavo di rete RJ-45 da 1 GbE per la porta 1  </li><li> Per la porta 3, la porta 4, la porta 5 o la porta 6, almeno 1 X 25-GbE. cavo di rame</li>| Il cliente deve acquistare questi cavi.<br>Per un elenco completo dei cavi di rete supportati, dei commutatori e dei ricetrasmettitori per le schede di rete del dispositivo, vedere la pagina relativa alla [matrice di interoperabilità Cavium FastlinQ serie 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e ai [prodotti compatibili con la scheda di rete Mellanox Dual Port 25](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Prima connessione del dispositivo      | Per la connessione iniziale la porta 1 dispone di un indirizzo IP fisso (192.168.100.10/24). <li>Richiedere un computer portatile per la connessione diretta alla porta 1, con un indirizzo IP nella rete 192.168.100.0/24.</li><li> Usa l'interfaccia utente locale del dispositivo in: `https://192.168.100.10` per un'ulteriore configurazione.</li><li> Al termine dell'installazione iniziale, è necessario usare almeno 1 GbE per il dispositivo. L'interfaccia utente Web locale non sarà accessibile se il Commuter connesso non è almeno 1 GbE.</li>|                                                                                                                   |
| Accesso al dispositivo                      | La password di amministratore del dispositivo deve avere una lunghezza compresa tra 8 e 16 caratteri. <br>e deve contenere tre dei seguenti tipi di carattere: maiuscolo, minuscolo, numerico e speciale.                                            | La password predefinita è *Password1* , che scade al primo accesso.                                                     |
| Impostazioni di rete                  | Il dispositivo è dotato di porte di rete da 2 x 1 GbE, 4 x 25 GbE. <li>La porta 1 viene utilizzata per configurare solo le impostazioni di gestione. Una o più porte dati possono essere connesse e configurate. </li><li> Almeno un'interfaccia di rete dati da una porta 2 alla 6 deve essere connessa a Internet (con connettività ad Azure).</li><li> Le impostazioni IP supportano la configurazione DHCP/IPv4 statica. | Per la configurazione IPv4 statica sono necessari IP, server DNS e gateway predefinito.                                                                                                                  |
| Impostazioni di rete di calcolo     | <li>Richiedere 2 indirizzi IP pubblici statici per i nodi Kubernetes e almeno 1 IP statico per il servizio Hub di Azure Stack Edge per accedere ai moduli di calcolo.</li><li>Richiedere un indirizzo IP per ogni servizio o contenitore aggiuntivo a cui è necessario accedere esternamente dall'esterno del cluster Kubernetes.</li>                                                                                                                       | È supportata solo la configurazione IPv4 statica.                                                                      |
| Opzionale Impostazioni proxy Web     | <li>IP/FQDN server proxy Web, porta </li><li>Nome utente proxy Web, password</li>                                                                                                                                                                                                    | Attualmente non supportato con l'installazione di calcolo.                                                                     |
| Impostazioni del firewall e della porta        | Usare i [modelli e le porte degli URL elencati](azure-stack-edge-system-requirements.md#networking-port-requirements) per consentire gli indirizzi IP del dispositivo.                                                                                                                                                  |                                                                                                                   |
| Opzionale Indirizzo MAC            | Se è necessario inserire nell'elenco elementi consentiti l'indirizzo MAC, ottenere l'indirizzo della porta connessa dall'interfaccia utente locale del dispositivo. |                                                                                                                   |
| Opzionale Porta di commutazione di rete    | Il dispositivo ospita macchine virtuali Hyper-V per il calcolo. Per impostazione predefinita, alcune configurazioni di porte del commutere di rete non sono adatte.                                                                                                        |                                                                                                                   |
| Consigliabile Impostazioni temporali       | Configurare il fuso orario, il server NTP primario, il server NTP secondario.                                                                                                                                                                    | Configurare il server NTP primario e secondario nella rete locale.<br>Se il server locale non è disponibile, è possibile configurare i server NTP pubblici.                                                    |
| Opzionale Aggiornare le impostazioni del server | <li>Richiedi indirizzo IP del server di aggiornamento nella rete locale, percorso del server WSUS. </li> | Per impostazione predefinita, viene usato il server pubblico di Windows Update.|
| Impostazioni del dispositivo                   | <li>Nome dispositivo registrato all'interno dell'organizzazione DNS </li><li>Dominio DNS</li> |                                                                                                                   |
| Opzionale Certificati                      | Usare i certificati generati dal dispositivo <br><br> Se si importano i propri certificati, è necessario: <li>Certificato di firma radice attendibile nel formato DER con *estensione cer* </li><li>Certificati endpoint in formato *PFX*</li>|I certificati dell'endpoint includono per Azure Resource Manager, archiviazione BLOB, interfaccia utente Web locale.                                                                                                                   |
| Attivazione                        | Richiedere la chiave di attivazione dalla risorsa Azure Stack Edge/Data Box Gateway.                                                                                                                                                       | Una volta generato, la chiave scade tra 3 giorni.                                                                        |


## <a name="next-steps"></a>Passaggi successivi

Preparare la distribuzione del [dispositivo Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md).



