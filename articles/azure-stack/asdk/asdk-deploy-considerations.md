---
title: Azure prerequisiti di distribuzione (ASDK) del Kit di sviluppo dello Stack | Documenti Microsoft
description: Esaminare i requisiti hardware e di ambiente per Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: f4b55bb3287f67792b3257c3f62256437f5625ca
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-deployment-planning-considerations"></a>Distribuzione di Azure Stack considerazioni sulla pianificazione
Prima di distribuire il Kit di sviluppo dello Stack di Azure (ASDK), verificare che il computer host di kit sviluppo soddisfi i requisiti descritti in questo articolo.


## <a name="hardware"></a>Hardware
| Componente | Minima | Consigliato |
| --- | --- | --- |
| Unità disco: sistema operativo |1 disco del sistema operativo con un minimo di 200 GB per la partizione di sistema (unità SSD o HDD) |1 disco del sistema operativo con un minimo di 200 GB per la partizione di sistema (unità SSD o HDD) |
| Unità disco: dati kit sviluppo generale<sup>*</sup>  |4 dischi. Ciascun disco offre una capacità minima di 140 GB (unità SSD o HDD). Vengono utilizzati tutti i dischi disponibili. |4 dischi. Ogni disco fornisce un minimo di 250 GB di capacità (unità SSD o HDD). Vengono utilizzati tutti i dischi disponibili. |
| Calcolo: CPU |Dual-Socket: 12 core fisici (totale) |Dual-Socket: 16 core fisici (totale) |
| Calcolo: memoria |96 GB di RAM |128 GB di RAM (questo è il requisito minimo per supportare i provider di risorse PaaS).|
| Calcolo: BIOS |Hyper-V abilitato (con supporto SLAT) |Hyper-V abilitato (con supporto SLAT) |
| Rete: NIC |Windows Server 2012 R2 certificazione necessaria per la scheda di rete; Nessuna funzionalità specializzata richiesta |Windows Server 2012 R2 certificazione necessaria per la scheda di rete; Nessuna funzionalità specializzata richiesta |
| Certificazione del logo HW |[Certificato per Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificate per Windows Server 2016](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> È necessario più di questa capacità è consigliata se si prevede di aggiunta di molte del [elementi del marketplace](asdk-marketplace-item.md) da Azure.

**Configurazione delle unità disco dati:** tutte le unità dati devono essere dello stesso tipo (tutti SAS, SATA tutti o NVMe tutti) e capacità. Se si utilizzano dischi SAS, le unità disco deve essere collegate tramite un unico percorso (non viene fornito alcun MPIO, supporto di percorsi multipli).

**Opzioni di configurazione HBA**

* (Scelta consigliata) HBA semplice
* RAID HBA-scheda deve essere configurato in modalità "pass-through"
* HBA RAID: i dischi devono essere configurati come dischi singoli, RAID-0

**Combinazioni di tipo di supporto e bus supportate**

* Unità HDD SATA
* Unità HDD SAS
* Unità HDD RAID
* Unità SSD RAID (se il tipo di supporto non viene specificato/unknown<sup>*</sup>)
* Unità SSD SATA + Unità HDD SATA
* Unità SSD SAS + Unità HDD SAS
* NVMe

<sup>*</sup> Controller RAID senza funzionalità pass-through non è in grado di riconoscere il tipo di supporto. Tali controller di contrassegnare le unità disco rigido e unità SSD come non specificato. In tal caso, unità SSD viene utilizzata come archivio permanente anziché la memorizzazione nella cache i dispositivi. Pertanto, è possibile distribuire il kit di sviluppo su tali unità SSD.

**HBA di esempio**: LSI 9207-8i, LSI-9300-8i o LSI 9265 8i in modalità pass-through

Sono disponibili configurazioni OEM di esempio.

## <a name="operating-system"></a>Sistema operativo
|  | **Requisiti** |
| --- | --- |
| **Versione sistema operativo** |Windows Server 2012 R2 o versioni successive. La versione del sistema operativo non critica prima dell'avvio della distribuzione, come verrà avviare il disco rigido virtuale che è incluso nell'installazione di Azure Stack il computer host. Il sistema operativo e tutte le patch necessarie sono già integrate nell'immagine. Non usare le chiavi per attivare tutte le istanze di Windows Server utilizzate nel kit di sviluppo. |

> [!TIP]
> Dopo aver installato il sistema operativo, è possibile utilizzare il [controllo distribuzione per Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) per confermare che l'hardware soddisfi tutti i requisiti.

## <a name="account-requirements"></a>Requisiti dell'account
In genere, si distribuisce il kit di sviluppo con connessione internet, in cui è possibile connettersi a Microsoft Azure. In questo caso, è necessario configurare un account di Azure Active Directory (Azure AD) per distribuire il kit di sviluppo.

Se l'ambiente non è connesso a internet o che non si desidera usare Azure AD, è possibile distribuire Azure Stack utilizzando Active Directory Federation Services (ADFS). Il kit di sviluppo include proprie istanze di AD FS e i servizi di dominio Active Directory. Se si distribuisce questa opzione, non è necessario configurare gli account di anticipo.

>[!NOTE]
Se si distribuisce con l'opzione di ADFS, è necessario ridistribuire dello Stack di Azure per passare ad Azure AD.

### <a name="azure-active-directory-accounts"></a>Account di Azure Active Directory
Per distribuire Azure Stack utilizzando un account di Azure AD, è necessario preparare un account Azure AD prima di eseguire la distribuzione di script di PowerShell. Questo account diventa l'amministratore globale per il tenant di Azure AD. Consente di eseguire il provisioning e il delegato applicazioni ed entità di servizio per tutti i servizi di Azure Stack che interagiscono con Azure Active Directory e l'API Graph. Viene inoltre utilizzato come il proprietario della sottoscrizione del provider predefinito (che è possibile modificare successivamente). È possibile accedere al portale dell'amministratore del sistema Azure Stack utilizzando questo account.

1. Creare un account di Azure AD che è l'amministratore di directory per Azure AD almeno uno. Se si dispone già di uno, è possibile utilizzarlo. In caso contrario, è possibile crearne uno gratuitamente al [ https://azure.microsoft.com/free/ ](http://azure.microsoft.com/pricing/free/) (in Cina, visitare <http://go.microsoft.com/fwlink/?LinkID=717821> invece). Se si prevede di in un secondo momento [registro dello Stack di Azure con Azure](asdk-register.md), è inoltre necessario avere una sottoscrizione in questo nuovo account.
   
    Salvare queste credenziali per l'utilizzo come l'amministratore del servizio. Questo account è possibile configurare e gestire i cloud di risorse, gli account utente, piani di tenant, quote e prezzi. Nel portale l'amministratore può creare piani, cloud di siti Web e cloud privati di macchine virtuali, nonché gestire le sottoscrizioni utente.
1. Creare account utente di almeno un test in Azure AD in modo che è possibile accedere al kit di sviluppo come tenant.
   
   | **Account di Azure Active Directory** | **Supportato?** |
   | --- | --- |
   | Account aziendale o dell'istituto di istruzione con sottoscrizione di Azure pubblico valido |Sì |
   | Account Microsoft con sottoscrizione pubblica di Azure valida |Sì |
   | Account aziendale o dell'istituto di istruzione con sottoscrizione di Azure valida Cina |Sì |
   | Account aziendale o dell'istituto di istruzione con valido US Government sottoscrizione di Azure |Sì |

## <a name="network"></a>Rete
### <a name="switch"></a>Switch
Una porta disponibile su un'opzione per la macchina kit di sviluppo.  

Il computer di sviluppo kit supporta la connessione a una porta di trunk o di porta del commutatore di accesso. Non è richiesta alcuna funzionalità specializzata nello switch. Se si usa una porta trunk o se si deve configurare un ID VLAN, è necessario specificare l'ID VLAN come parametro di distribuzione.

### <a name="subnet"></a>Subnet
Non si connettono computer kit di sviluppo per le seguenti subnet:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Queste subnet sono riservate per le reti interne all'interno dell'ambiente di kit di sviluppo.

### <a name="ipv4ipv6"></a>IPv4/IPv6
È supportato solo il protocollo IPv4. Non è possibile creare reti IPv6.

### <a name="dhcp"></a>DHCP
Assicurarsi che sia disponibile un server DHCP nella rete a cui si connette la scheda NIC. Se non è disponibile un server DHCP, è necessario preparare un'altra rete IPv4 statica oltre a quella usata dall'host. È necessario fornire l'indirizzo IP e gateway come un parametro di distribuzione.

### <a name="internet-access"></a>Accesso a Internet
Stack di Azure richiede l'accesso a Internet, direttamente o tramite un proxy trasparente. Stack di Azure non supporta la configurazione di un proxy web per abilitare l'accesso a Internet. L'IP dell'host sia il nuovo indirizzo IP assegnato al BGPNAT01 di definiti, tramite DHCP o indirizzo IP statico, deve essere in grado di accedere a Internet. Con i domini graph.windows.net e login.microsoftonline.com vengono utilizzate porte 80 e 443.


## <a name="next-steps"></a>Passaggi successivi
[Scaricare il pacchetto di distribuzione ASDK](asdk-download.md)
