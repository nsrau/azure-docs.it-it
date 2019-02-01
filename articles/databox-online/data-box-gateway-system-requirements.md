---
title: Requisiti di sistema di Microsoft Azure Data Box Gateway | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: alkohli
ms.openlocfilehash: 81df0a776cd22490342230567deacb23097cd12e
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094190"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Requisiti di sistema di Azure Data Box Gateway (anteprima)

Questo articolo descrive i requisiti di sistema importanti per la soluzione Microsoft Azure Data Box Gateway e per i client che ci connettono ad Azure Data Box Gateway. Prima di distribuire Data Box Gateway è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema per il dispositivo virtuale Data Box Gateway includono:

- **Requisiti software per gli host:** descrizione delle piattaforme supportate, dei browser per l'interfaccia utente di configurazione locale, dei client SMB e degli eventuali requisiti aggiuntivi per gli host che si connettono al dispositivo.
- **Requisiti di rete per il dispositivo:** informazioni sui requisiti di rete per il funzionamento ottimale del dispositivo virtuale.

> [!IMPORTANT]
> Data Box Gateway è in anteprima. Prima di distribuire la soluzione, leggere le [condizioni d'uso per l'anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="specifications-for-the-virtual-device"></a>Specifiche per il dispositivo virtuale

Il sistema host sottostante per Data Box Gateway deve essere in grado di dedicare le risorse seguenti al provisioning del dispositivo virtuale:

| Specifiche                                          | DESCRIZIONE              |
|---------------------------------------------------------|--------------------------|
| Processori virtuali (core)   | Minimo 4 |            
| Memoria  | Minimo 8 GB|
| Disponibilità|Nodo singolo|
| Dischi| Disco sistema operativo: 250 GB <br> Disco dati: almeno 2 TB, con thin provisioning e supportato da unità SSD|
| Interfacce di rete|1 o più interfacce di rete virtuali|


## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo supportato per i client connessi al dispositivo

Di seguito è riportato l'elenco dei sistemi operativi supportati per i client o gli host connessi a Data Box Gateway.

| **Sistema operativo/piattaforma** | **Versioni** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 <br> 2019 |
|  Windows |8, 10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolli supportati per i client che accedono al dispositivo

|**Protocollo** |**Versioni**   |**Note**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | SMB 1 non è supportato.|
|NFS     | V3 e V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Piattaforme di virtualizzazione supportate per il dispositivo

| **Sistema operativo/piattaforma**  |**Versioni**   |**Note**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6,5 <br> 6.7       |Gli strumenti VMware non sono supportati.         |


## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Ecco un elenco degli account di archiviazione supportati per Data Box Gateway.

| **Account di archiviazione** | **Note** |
| --- | --- |
| Classico | Standard |
| Utilizzo generico  |Standard; sono supportati sia V1 che V2. Sono supportati livelli ad accesso frequente e sporadico. |


## <a name="supported-storage-types"></a>Tipi di archiviazione supportati

Di seguito è riportato un elenco dei tipi di archiviazione supportati per Data Box Gateway.

| **Formato file** | **Note** |
| --- | --- |
| BLOB in blocchi di Azure | |
| BLOB di pagine di Azure  | |
| File di Azure | |

## <a name="supported-browsers-for-local-web-ui"></a>Browser supportati per l'interfaccia utente Web locale

Di seguito è riportato un elenco dei browser supportati per l'interfaccia utente Web locale per il dispositivo virtuale.

|Browser  |Versioni  |Requisiti aggiuntivi/note  |
|---------|---------|---------|
|Google Chrome   |Versione più recente         |         |
|Microsoft Edge    | Versione più recente        |         |
|Internet Explorer     | Versione più recente        |         |
|FireFox    |Versione più recente         |         |


## <a name="networking-requirements"></a>Requisiti di rete

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB, cloud o di gestione. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Est* o *in uscita* fa riferimento alla direzione in cui il dispositivo Data Box Gateway invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

| N. porta| In ingresso/In uscita | Ambito porta| Obbligatoria|   Note                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|In uscita|WAN |No |La porta in uscita viene usata per consentire all'accesso Internet di recuperare gli aggiornamenti. <br>Il proxy Web in uscita è configurabile dall'utente. |                          
| TCP 443 (HTTPS)|In uscita|WAN|Yes|La porta in uscita viene usata per accedere ai dati nel cloud.<br>Il proxy Web in uscita è configurabile dall'utente.|   
| UDP 53 (DNS)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DNS basato su Internet.<br>È consigliabile l'uso del server DNS locale. |
| UDP 123 (NTP)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server NTP basato su Internet.  |
| UDP 67 (DHCP)|In uscita|WAN|In alcuni casi<br>Vedere le note|Questa porta è obbligatoria solo se si usa un server DHCP.  |
| TCP 80 (HTTP)|In ingresso|LAN|Yes|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. <br>L'accesso all'interfaccia utente locale tramite HTTP esegue il reindirizzamento automatico a HTTPS.  | 
| TCP 443 (HTTPS)|In ingresso|LAN|Yes|Questa è la porta in ingresso per l'interfaccia utente locale nel dispositivo per la gestione locale. | 

## <a name="url-patterns-for-firewall-rules"></a>Modelli URL per le regole del firewall

Gli amministratori di rete possono spesso configurare regole del firewall avanzate in base ai modelli URL in modo da filtrare il traffico in entrata e in uscita. Il dispositivo Data Box Gateway e il servizio Data Box Gateway dipendono da altre applicazioni Microsoft, ad esempio il bus di servizio di Azure, il controllo di accesso di Azure Active Directory, gli account di archiviazione e i server di Microsoft Update. I modelli URL associati a queste applicazioni possono essere usati per configurare le regole del firewall. È importante comprendere che i modelli di URL associati alle suddette applicazioni possono variare. Questo a sua volta richiederà, da parte dell'amministratore di rete, il monitoraggio e l'aggiornamento delle regole del firewall per Data Box Gateway a seconda delle esigenze.

È consigliabile impostare le regole del firewall per il traffico in uscita, liberamente nella maggior parte dei casi, in base agli indirizzi IP fissi Data Box Gateway. Tuttavia, è possibile utilizzare le informazioni seguenti per impostare regole del firewall avanzate indispensabili per creare ambienti protetti.

> [!NOTE]
> - Gli indirizzi IP di origine del dispositivo devono essere sempre impostati su tutte le interfacce di rete abilitate per il cloud.
> - Gli indirizzi IP di destinazione devono essere impostati sugli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Modello URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Componente/funzionalità                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Servizio Azure Data Box Gateway<br>Bus di servizio di Azure<br>Servizio di autenticazione    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Attivazione del dispositivo                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Revoca del certificato                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Account di archiviazione di Azure e monitoraggio                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Server di Microsoft Update                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Rete CDN di Akamai                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Pacchetto di supporto                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Servizio Telemetria in Windows; vedere Aggiornamento per la soddisfazione dei clienti e di telemetria diagnostica      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Larghezza di banda Internet

Per la larghezza di banda Internet minima disponibile per i dispositivi Data Box Gateway si applicano i requisiti seguenti.

- Data Box Gateway deve disporre di una larghezza di banda Internet dedicata a 20 Mbps (o superiore) sempre disponibile. La larghezza di banda non deve essere condivisa con altre applicazioni. 
- Data Box Gateway deve disporre di una larghezza di banda Internet dedicata 32 Mbps (o superiore) quando si usa la limitazione della larghezza di banda della rete.

## <a name="next-step"></a>Passaggio successivo

* [Distribuire Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

