---
title: Abilitare Crittografia dischi di Azure per macchine virtuali Linux
description: Questo articolo offre informazioni sull'abilitazione di Crittografia dischi di Microsoft Azure per le macchine virtuali Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 75e469b30632bb7e7e8f6445db78acda784ac5da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601276"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Crittografia dischi di Azure per macchine virtuali Linux 

Crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Usa la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per offrire la crittografia del volume per il disco del sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con [Azure Key Vault](../../key-vault/index.yml) per facilitare il controllo e la gestione delle chiavi e dei segreti di crittografia dei dischi. 

Se si usa [Centro sicurezza di Azure](../../security-center/index.yml), si viene avvisati se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se in precedenza è stato usato Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per altre informazioni, vedere [Crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-overview-aad.md). 
> - Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione. Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.
> - Attualmente le macchine virtuali di seconda generazione non supportano Crittografia dischi di Azure. Per altre informazioni, vedere [Supporto per le macchine virtuali di seconda generazione in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2).

È possibile apprendere in pochi minuti le nozioni di base di Crittografia dischi di Azure per Linux con [Avvio rapido: Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](disk-encryption-cli-quickstart.md) o [Avvio rapido: Creare e crittografare una macchina virtuale Linux con Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Macchine virtuali e sistemi operativi supportati

### <a name="supported-vms"></a>VM supportate

Le macchine virtuali Linux sono disponibili in una [gamma di dimensioni](sizes.md). Crittografia dischi di Azure non è disponibile nelle [macchine virtuali serie A di tipo Basic](https://azure.microsoft.com/pricing/details/virtual-machines/series/) o in quelle che non soddisfano questi requisiti di memoria minimi:

| Macchina virtuale | Requisito di memoria minimo |
|--|--|
| Macchine virtuali Linux quando si usa la crittografia solo per i volumi di dati| 2 GB |
| Macchine virtuali Linux quando si usa la crittografia per i volumi di dati e del sistema operativo e nei casi in cui l'utilizzo del file system radice (/) è di 4 GB o inferiore | 8 GB |
| Macchine virtuali Linux quando si usa la crittografia per i volumi di dati e del sistema operativo e nei casi in cui l'utilizzo del file system radice (/) è superiore a 4 GB | Utilizzo del file system radice x 2. Ad esempio, per un utilizzo del file system radice di 16 GB sono necessari almeno 32 GB di RAM |

Al termine del processo di crittografia del disco del sistema operativo nelle macchine virtuali Linux, la macchina virtuale può essere configurata per l'esecuzione con una minore quantità di memoria. 

Crittografia dischi di Azure è disponibile anche per le macchine virtuali con Archiviazione Premium.

Crittografia dischi di Azure non è disponibile nelle [macchine virtuali di seconda generazione](generation-2.md#generation-1-vs-generation-2-capabilities)) e nelle [macchine virtuali della serie Lsv2](../lsv2-series.md)). Per altre eccezioni, vedere [Crittografia dischi di Azure: Scenari non supportati](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Crittografia dischi di Azure è supportato in un sottoinsieme di [distribuzioni di Linux approvate in Azure](endorsed-distros.md) che è a sua volta un sottoinsieme di tutte le distribuzioni di server Linux possibili.

![Diagramma di Venn delle distribuzioni di server Linux che supportano Crittografia dischi di Azure](./media/disk-encryption/ade-supported-distros.png)

Le distribuzioni di server Linux non approvate da Azure non supportano Crittografia dischi di Azure. Tra quelle approvate, solo le distribuzioni e versioni seguenti supportano Crittografia dischi di Azure:

| Editore | Offerta | SKU | URN | Tipo di volume supportato per la crittografia |
| --- | --- |--- | --- |
| Canonical | Ubuntu | 18.04-LTS | Canonical:UbuntuServer:18.04-LTS:latest | Disco del sistema operativo e dati |
| Canonical | Ubuntu 18.04 | 18.04-DAILY-LTS | Canonical:UbuntuServer:18.04-DAILY-LTS:latest | Disco del sistema operativo e dati |
| Canonical | Ubuntu 16.04 | 16.04-DAILY-LTS | Canonical:UbuntuServer:16.04-DAILY-LTS:latest | Disco del sistema operativo e dati |
| Canonical | Ubuntu 14.04.5</br>[con il kernel ottimizzato per Azure aggiornato alla versione 4.15 o successiva](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical:UbuntuServer:14.04.5-LTS:latest | Disco del sistema operativo e dati |
| Canonical | Ubuntu 14.04.5</br>[con il kernel ottimizzato per Azure aggiornato alla versione 4.15 o successiva](disk-encryption-troubleshooting.md) | 14.04.5-DAILY-LTS | Canonical:UbuntuServer:14.04.5-DAILY-LTS:latest | Disco del sistema operativo e dati |
| RedHat | RHEL 7.7 | 7.7 | RedHat:RHEL:7.7:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.7 | 7-LVM | RedHat:RHEL:7-LVM:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.6 | 7.6 | RedHat:RHEL:7.6:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.5 | 7,5 | RedHat:RHEL:7.5:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.4 | 7.4 | RedHat:RHEL:7.4:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.3 | 7.3 | RedHat:RHEL:7.3:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 7.2 | 7.2 | RedHat:RHEL:7.2:latest | Disco del sistema operativo e dati (vedere la nota di seguito) |
| RedHat | RHEL 6.8 | 6.8 | RedHat:RHEL:6.8:latest | Disco dati (vedere la nota di seguito) |
| RedHat | RHEL 6.7 | 6.7 | RedHat:RHEL:6.7:latest | Disco dati (vedere la nota di seguito) |
| OpenLogic | CentOS 7.7 | 7.7 | OpenLogic:CentOS:7.7:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.7 | 7-LVM | OpenLogic:CentOS:7-LVM:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.6 | 7.6 | OpenLogic:CentOS:7.6:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic:CentOS:7.5:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic:CentOS:7.4:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic:CentOS:7.3:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.2n | 7.2n | OpenLogic:CentOS:7.2n:latest | Disco del sistema operativo e dati |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic:CentOS:7.1:latest | Solo il disco dati |
| OpenLogic | CentOS 7.0 | 7.0 | OpenLogic:CentOS:7.0:latest | Solo il disco dati |
| OpenLogic | CentOS 6.8 | 6.8 | OpenLogic:CentOS:6.8:latest | Solo il disco dati |
| SUSE | openSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | Solo il disco dati |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE:SLES:12-SP4:latest | Solo il disco dati |
| SUSE | SLES HPC 12-SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | Solo il disco dati |

> [!NOTE]
> La nuova implementazione di Crittografia dischi di Azure è supportata per il disco del sistema operativo RHEL e il disco dati per le immagini RHEL7 con pagamento in base al consumo.  
>
> Crittografia dischi di Azure è supportato anche per le immagini Gold di tipo BYOS (Bring-Your-Own-Subscription) di RHEL, ma solo **dopo** la registrazione della sottoscrizione. Per altre informazioni, vedere [Immagini Gold di tipo BYOS (Bring-Your-Own-Subscription) di Red Hat Enterprise Linux in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisiti aggiuntivi delle macchine virtuali

Crittografia dischi di Azure richiede la presenza nel sistema dei moduli dm-crypt e vfat. La rimozione o la disabilitazione di vfat dall'immagine predefinita impedirà al sistema di leggere la chiave del volume e ottenere quella necessaria per sbloccare i dischi ai successivi riavvii. I passaggi per la protezione avanzata del sistema che rimuovono il modulo VFAT dal sistema o applicano l'espansione delle montaggio/cartelle del sistema operativo nelle unità dati non sono compatibili con crittografia dischi di Azure. 

Prima di abilitare la crittografia, i dischi dati da crittografare devono essere elencati correttamente in /etc/fstab. Durante la creazione delle voci usare l'opzione "nofail" e scegliere un nome di dispositivo a blocchi permanente. I nomi di dispositivo nel formato "/dev/sdX" potrebbero infatti non essere associati allo stesso disco nei vari riavvii, in particolare dopo la crittografia. Per altre informazioni su questo comportamento, vedere [Risolvere il problema dei cambi di nome dei dispositivi nelle macchine virtuali Linux](troubleshoot-device-names-problems.md).

Verificare che le impostazioni /etc/fstab siano configurate correttamente per il montaggio. Per configurare queste impostazioni, eseguire il comando mount -a o riavviare la macchina virtuale e attivare il rimontaggio in questo modo. Al termine, controllare l'output del comando lsblk per verificare che l'unità desiderata sia ancora montata. 

- Se il file /etc/fstab non monta l'unità in modo corretto prima di abilitare la crittografia; Crittografia dischi di Azure non sarà in grado di montarla correttamente.
- Il processo di Crittografia dischi di Azure sposterà le informazioni di montaggio da /etc/fstab e le inserirà in un proprio file di configurazione come parte del processo di crittografia. Non allarmarsi per la mancanza della voce da /etc/fstab dopo il completamento della crittografia dell'unità dati.
- Prima di avviare la crittografia, assicurarsi di arrestare tutti i servizi e i processi che potrebbero scrivere nei dischi dati montati e disabilitarli, in modo che non vengano riavviati automaticamente dopo un riavvio. Questi potrebbero infatti tenere aperti i file presenti in queste partizioni impedendo alla procedura di crittografia di rimontarle, determinando così un errore di crittografia. 
- Dopo il riavvio, il processo di Crittografia dischi di Azure avrà bisogno di tempo per montare i dischi appena crittografati. Non saranno disponibili immediatamente dopo un riavvio. Il processo richiede tempo per iniziare, sbloccare e quindi montare le unità crittografate prima che siano disponibili per l'accesso da parte di altri processi. Questo processo potrebbe richiedere più di un minuto dopo il riavvio, a seconda delle caratteristiche di sistema.

Di seguito è riportato un esempio dei comandi usati per montare i dischi dati e creare le voci /etc/fstab necessarie:

```bash
UUID0="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun0)"
UUID1="$(blkid -s UUID -o value /dev/disk/azure/scsi1/lun1)"
mkdir /data0
mkdir /data1
echo "UUID=$UUID0 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "UUID=$UUID1 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
## <a name="networking-requirements"></a>Requisiti di rete

Per abilitare la funzionalità Crittografia dischi di Azure, le macchine virtuali Linux devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, è necessario che la macchina virtuale Linux possa connettersi a un endpoint di Azure Active Directory, \[login.microsoftonline.com\].
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale Linux possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La macchina virtuale Linux deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia  

Crittografia dischi di Azure richiede Azure Key Vault per controllare e gestire segreti e chiavi di crittografia dei dischi. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.

Per altre informazioni, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione di Crittografia dischi di Azure:

| Terminologia | Definizione |
| --- | --- |
| Insieme di credenziali chiave di Azure | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) è il sottosistema di crittografia del disco trasparente basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali Linux. |
| Chiave di crittografia della chiave (KEK) | La chiave asimmetrica (RSA 2048) che è possibile usare per proteggere il segreto o eseguirne il wrapping. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure ](disk-encryption-cli-quickstart.md)
- [Avvio rapido: Creare e crittografare una macchina virtuale Linux con Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-linux.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell dei prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](disk-encryption-key-vault.md)


