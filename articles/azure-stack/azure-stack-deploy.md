---
title: Prerequisiti per la distribuzione di Azure SDK Stack | Documenti Microsoft
description: Consente di visualizzare i requisiti hardware e di ambiente per Azure Stack Development Kit (operatore cloud).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 32a21d9b-ee42-417d-8e54-98a7f90f7311
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: jeffgilb
ms.openlocfilehash: 8a0d23e14ef50034d5f9595cf154c3513a09c464
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-stack-deployment-prerequisites"></a>Prerequisiti per la distribuzione di Azure Stack

*Si applica a: Azure Stack Development Kit*

Prima di distribuire [Kit di sviluppo di Azure Stack](azure-stack-poc.md), assicurarsi che il computer soddisfi i requisiti seguenti:


## <a name="hardware"></a>Hardware
| Componente | Minima | Consigliato |
| --- | --- | --- |
| Unità disco: sistema operativo |1 disco del sistema operativo con un minimo di 200 GB per la partizione di sistema (unità SSD o HDD) |1 disco del sistema operativo con un minimo di 200 GB per la partizione di sistema (unità SSD o HDD) |
| Unità disco: generali per lo sviluppo kit dati * |4 dischi. Ciascun disco offre una capacità minima di 140 GB (unità SSD o HDD). Verranno utilizzati tutti i dischi disponibili. |4 dischi. Ogni disco fornisce un minimo di 250 GB di capacità (unità SSD o HDD). Verranno utilizzati tutti i dischi disponibili. |
| Calcolo: CPU |Dual-Socket: 12 core fisici (totale) |Dual-Socket: 16 core fisici (totale) |
| Calcolo: memoria |96 GB di RAM |128 GB di RAM (questo è il requisito minimo per supportare i provider di risorse PaaS).|
| Calcolo: BIOS |Hyper-V abilitato (con supporto SLAT) |Hyper-V abilitato (con supporto SLAT) |
| Rete: NIC |Windows Server 2012 R2 certificazione necessaria per la scheda di rete; Nessuna funzionalità specializzata richiesta |Windows Server 2012 R2 certificazione necessaria per la scheda di rete; Nessuna funzionalità specializzata richiesta |
| Certificazione del logo HW |[Certificato per Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificato per Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

\*Sarà necessario più di questa capacità è consigliata se si prevede di aggiungere molti di [elementi del marketplace](azure-stack-download-azure-marketplace-item.md) da Azure.

**Configurazione delle unità disco dati:** tutte le unità dati devono essere dello stesso tipo (tutti SAS, SATA tutti o NVMe tutti) e capacità. Se si utilizzano dischi SAS, le unità disco deve essere collegate tramite un unico percorso (non viene fornito alcun MPIO, supporto di percorsi multipli).

**Opzioni di configurazione HBA**

* (Scelta consigliata) HBA semplice
* RAID HBA-scheda deve essere configurato in modalità "pass-through"
* HBA RAID: i dischi devono essere configurati come dischi singoli, RAID-0

**Combinazioni di tipo di supporto e bus supportate**

* Unità HDD SATA
* Unità HDD SAS
* Unità HDD RAID
* SSD RAID (se il tipo di supporto non viene specificato/sconosciuto\*)
* Unità SSD SATA + Unità HDD SATA
* Unità SSD SAS + Unità HDD SAS
* NVMe

\*Controller RAID senza funzionalità pass-through non è in grado di riconoscere il tipo di supporto. Tali controller contrassegneranno le unità HDD e SSD come non specificate. In tal caso verrà usata l'unità SSD come archivio permanente anziché eseguire la memorizzazione nella cache dei dispositivi. Pertanto, è possibile distribuire il kit di sviluppo su tali unità SSD.

**HBA di esempio**: LSI 9207-8i, LSI-9300-8i o LSI 9265 8i in modalità pass-through

Sono disponibili configurazioni OEM di esempio.

## <a name="operating-system"></a>Sistema operativo
|  | **Requisiti** |
| --- | --- |
| **Versione sistema operativo** |Windows Server 2012 R2 o versioni successive. La versione del sistema operativo non critica prima dell'avvio della distribuzione, come verrà avviare il disco rigido virtuale che è incluso nell'installazione di Azure Stack il computer host. Il sistema operativo e tutte le patch necessarie sono già integrate nell'immagine. Non usare le chiavi per attivare tutte le istanze di Windows Server utilizzate nel kit di sviluppo. |

## <a name="deployment-requirements-check-tool"></a>Strumento di controllare i requisiti di distribuzione
Dopo aver installato il sistema operativo, è possibile utilizzare il [controllo distribuzione per Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) per confermare che l'hardware soddisfi tutti i requisiti.

## <a name="account-requirements"></a>Requisiti dell'account
In genere, si distribuisce il kit di sviluppo con connessione internet, in cui è possibile connettersi a Microsoft Azure. In questo caso, è necessario configurare un account di Azure Active Directory (Azure AD) per distribuire il kit di sviluppo.

Se l'ambiente non è connesso a internet o che non si desidera usare Azure AD, è possibile distribuire Azure Stack utilizzando Active Directory Federation Services (ADFS). Il kit di sviluppo include proprie istanze di AD FS e i servizi di dominio Active Directory. Se si distribuisce questa opzione, non è necessario configurare gli account di anticipo.

>[!NOTE]
Se si distribuisce con l'opzione di ADFS, è necessario ridistribuire dello Stack di Azure per passare ad Azure AD.

### <a name="azure-active-directory-accounts"></a>Account di Azure Active Directory
Per distribuire Azure Stack utilizzando un account di Azure AD, è necessario preparare un account Azure AD prima di eseguire la distribuzione di script di PowerShell. Questo account diventa l'amministratore globale per il tenant di Azure AD. Consente di eseguire il provisioning e il delegato applicazioni ed entità di servizio per tutti i servizi di Azure Stack che interagiscono con Azure Active Directory e l'API Graph. Viene inoltre utilizzato come il proprietario della sottoscrizione del provider predefinito (che è possibile modificare successivamente). È possibile accedere al portale dell'amministratore del sistema Azure Stack utilizzando questo account.

1. Creare un account di Azure AD che è l'amministratore di directory per Azure AD almeno uno. Se si dispone già di uno, è possibile utilizzarlo. In caso contrario, è possibile crearne uno gratuitamente a [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (in Cina, visitare <http://go.microsoft.com/fwlink/?LinkID=717821> invece). Se si prevede di in un secondo momento [registro dello Stack di Azure con Azure](azure-stack-register.md), è inoltre necessario avere una sottoscrizione in questo nuovo account.
   
    Salvare queste credenziali per l'utilizzo nel passaggio 6 di [distribuire il kit di sviluppo](azure-stack-run-powershell-script.md#deploy-the-development-kit). Questo *amministratore del servizio* account è possibile configurare e gestire cloud di risorse, account utente, piani di tenant, quote e prezzi. Nel portale l'amministratore può creare piani, cloud di siti Web e cloud privati di macchine virtuali, nonché gestire le sottoscrizioni utente.
2. [Creare](azure-stack-add-new-user-aad.md) almeno un account in modo che è possibile accedere al kit di sviluppo come tenant.
   
   | **Account di Azure Active Directory** | **Supportato?** |
   | --- | --- |
   | Account aziendale o dell'istituto di istruzione con sottoscrizione di Azure pubblico valido |Sì |
   | Account Microsoft con sottoscrizione pubblica di Azure valida |Sì |
   | Account aziendale o dell'istituto di istruzione con sottoscrizione di Azure valida Cina |Sì |
   | Account aziendale o dell'istituto di istruzione con valido US Government sottoscrizione di Azure |Sì |

## <a name="network"></a>Rete
### <a name="switch"></a>Switch
Una porta disponibile su un'opzione per la macchina kit di sviluppo.  

Il computer di sviluppo kit supporta la connessione a una porta di trunk o di porta del commutatore di accesso. Non è richiesta alcuna funzionalità specializzata nello switch. Se si usa una porta trunk o se si deve configurare un ID VLAN, è necessario specificare l'ID VLAN come parametro di distribuzione. È possibile visualizzare esempi di [elenco di parametri di distribuzione](azure-stack-run-powershell-script.md).

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
Assicurarsi che sia disponibile un server DHCP nella rete a cui si connette la scheda NIC. Se non è disponibile un server DHCP, è necessario preparare un'altra rete IPv4 statica oltre a quella usata dall'host. È necessario fornire l'indirizzo IP e gateway come un parametro di distribuzione. È possibile visualizzare esempi di [elenco di parametri di distribuzione](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Accesso a Internet
Stack di Azure richiede l'accesso a Internet, direttamente o tramite un proxy trasparente. Stack di Azure non supporta la configurazione di un proxy web per abilitare l'accesso a Internet. L'IP dell'host sia il nuovo indirizzo IP assegnato al BGPNAT01 di definiti, tramite DHCP o indirizzo IP statico, deve essere in grado di accedere a Internet. Con i domini graph.windows.net e login.microsoftonline.com vengono utilizzate porte 80 e 443.

## <a name="telemetry"></a>Telemetria

Dati di telemetria contribuiscono a forma di versioni future dello Stack di Azure. Consente a rispondere rapidamente ai commenti e suggerimenti, offre nuove funzionalità e migliorare la qualità. Microsoft Azure Stack include Windows Server 2016 e SQL Server 2014. Nessuno di questi prodotti vengono modificate dalle impostazioni predefinite e sono descritti per l'informativa sulla Privacy di Microsoft Enterprise. Stack di Azure contiene anche il software open source che non è stato modificato per inviare dati di telemetria a Microsoft. Di seguito sono riportati alcuni esempi di dati di telemetria dello Stack di Azure:

- informazioni sulla registrazione di distribuzione
- Quando un avviso viene aperto e chiuso
- il numero di risorse di rete

Per supportare il flusso di dati di telemetria, la porta 443 (HTTPS) deve essere aperta nella rete. L'endpoint client è https://vortex-win.data.microsoft.com.

Se non si desidera fornire dati di telemetria per lo Stack di Azure, è possibile disattivare, l'host del kit di sviluppo e le macchine virtuali di infrastruttura come illustrato di seguito.

### <a name="turn-off-telemetry-on-the-development-kit-host-optional"></a>Disattivare la telemetria nell'host di kit di sviluppo (facoltativo)

>[!NOTE]
Se si desidera disattivare la telemetria per l'host del kit di sviluppo, è necessario farlo prima di eseguire lo script di distribuzione.

Prima di [l'esecuzione dello script asdk installer.ps1]() per distribuire l'host del kit di sviluppo, l'avvio di CloudBuilder.vhdx ed eseguire lo script seguente in una finestra di PowerShell con privilegi elevata:
```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0'  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Impostazione **AllowTelemetry** su 0, verranno disattivate telemetria per la distribuzione di Windows e Stack di Azure. Vengono inviati solo eventi di sicurezza critiche dal sistema operativo. L'impostazione di controlli dati di telemetria di Windows in tutti gli host e macchine virtuali di infrastruttura e viene riapplicato a nodi di nuovo le macchine virtuali quando vengono eseguite operazioni di scalabilità orizzontale.


### <a name="turn-off-telemetry-on-the-infrastructure-virtual-machines-optional"></a>Disattivare la telemetria nelle macchine virtuali dell'infrastruttura (facoltativo)

Dopo la distribuzione ha esito positivo, eseguire lo script seguente in una finestra di PowerShell con privilegi elevata (come l'utente AzureStack\AzureStackAdmin) nell'host di kit di sviluppo:

```powershell
$AzSVMs= get-vm |  where {$_.Name -like "AzS-*"}
### Show current AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
### Set & Get updated AllowTelemetry value for all AzS-VMs
invoke-command -computername $AzSVMs.name {Set-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name "AllowTelemetry" -Value '0'}
invoke-command -computername $AzSVMs.name {(Get-ItemProperty -Path `
"HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" -Name AllowTelemetry).AllowTelemetry}
```

Per configurare i dati di telemetria di SQL Server, vedere [come configurare SQL Server 2016](https://support.microsoft.com/en-us/help/3153756/how-to-configure-sql-server-2016-to-send-feedback-to-microsoft).

### <a name="usage-reporting"></a>Report sull'utilizzo

Tramite la registrazione, Stack di Azure è configurato anche per informazioni sull'utilizzo di inoltro a Azure. Report sull'utilizzo viene controllato in modo indipendente da dati di telemetria. È possibile disattivare la segnalazione quando utilizzo [registrazione](azure-stack-register.md) usando lo script su Github. È sufficiente impostare la **$reportUsage** parametro **$false**.

Dati di utilizzo vengono formattati come descritto nel [dati di utilizzo dello Stack di Azure di Report in Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-usage-reporting). Gli utenti di Azure il Kit di sviluppo dello Stack non vengono addebitati effettivamente. Questa funzionalità è inclusa nel kit di sviluppo in modo da poter testare per verificare il funzionamento del report di utilizzo. 


## <a name="next-steps"></a>Passaggi successivi
[Scaricare il pacchetto di distribuzione kit di sviluppo di Azure Stack](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Distribuire il kit di sviluppo di Azure Stack](azure-stack-run-powershell-script.md)

