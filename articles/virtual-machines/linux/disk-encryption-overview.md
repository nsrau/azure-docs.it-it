---
title: Abilitare Crittografia dischi di Azure per macchine virtuali Linux
description: Questo articolo fornisce istruzioni sull'abilitazione di Crittografia disco di Microsoft Azure per macchine virtuali Linux.This article provides instructions on enabling Microsoft Azure Disk Encryption for Linux VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fa7e085f723d4f4c411f52e045c9437d5cb293b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459781"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption for Linux VMs 

Crittografia dischi di Azure consente di proteggere i dati per soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Usa la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con [L'insieme](../../key-vault/index.yml) di credenziali delle chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco. 

Se si usa [il Centro sicurezza](../../security-center/index.yml)di Azure , si viene avvisati se sono disponibili macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se in precedenza è stato usato Crittografia disco di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere Crittografia del disco di [Azure con Azure AD (versione precedente).](disk-encryption-overview-aad.md) 
> - Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione. Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.
> - Attualmente le macchine virtuali di generazione 2 non supportano Crittografia disco di Azure.Currently Generation 2 VMs do not support Azure Disk Encryption. Per informazioni [dettagliate, vedere Supporto per macchine virtuali di generazione 2 in Azure.See Support for Generation 2 VMs on Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) for details.

È possibile apprendere le nozioni di base di Crittografia disco di Azure per Linux in pochi minuti con la guida [introduttiva Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga](disk-encryption-cli-quickstart.md) di comando di Azure o [creare e crittografare una macchina virtuale Linux con la guida introduttiva](disk-encryption-powershell-quickstart.md)di Azure Powershell .

## <a name="supported-vms-and-operating-systems"></a>Macchine virtuali e sistemi operativi supportati

### <a name="supported-vms"></a>VM supportate

Le macchine virtuali Linux sono disponibili in una [gamma di dimensioni.](sizes.md) Crittografia disco di Azure non è disponibile nelle macchine virtuali di base, serie A o in macchine virtuali che non soddisfano i requisiti minimi di memoria seguenti:Azure Disk Encryption is not available on [Basic, A-series VMs,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)or on virtual machines that do not meet these minimum memory requirements:

| Macchina virtuale | Requisito minimo di memoria |
|--|--|
| Macchine virtuali Linux quando si crittografano solo i volumi di datiLinux VMs when only encrypting data volumes| 2 GB |
| Macchine virtuali Linux durante la crittografia di dati e volumi del sistema operativo e in cui l'utilizzo del file system radice (/) è pari o inferiore a 4 GB | 8 GB |
| Macchine virtuali Linux durante la crittografia di dati e volumi del sistema operativo e in cui l'utilizzo del file system radice (/) è superiore a 4 GB | Utilizzo del file system radice: 2. Ad esempio, un utilizzo del file system radice di 16 GB richiede almeno 32 GB di RAM |

Una volta completato il processo di crittografia del disco del sistema operativo nelle macchine virtuali Linux, la macchina virtuale può essere configurata per l'esecuzione con meno memoria. 

Crittografia disco di Azure è disponibile anche per le macchine virtuali con archiviazione Premium.Azure Disk Encryption is also available for VMs with premium storage.

Crittografia disco di Azure non è disponibile nelle [macchine virtuali di generazione 2](generation-2.md#generation-1-vs-generation-2-capabilities)e nelle macchine virtuali serie [Lsv2](../lsv2-series.md). Per altre eccezioni, vedere Crittografia disco di [Azure: scenari non supportati.](disk-encryption-linux.md#unsupported-scenarios)

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Crittografia disco di Azure è supportata in un sottoinsieme delle [distribuzioni Linux approvate da Azure,](endorsed-distros.md)che a sua volta è un sottoinsieme di tutte le possibili distribuzioni del server Linux.

![Venn Diagram of Linux server distributions that support Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Le distribuzioni di server Linux non approvate da Azure non supportano Crittografia disco di Azure. Di quelli approvati, solo le distribuzioni e le versioni seguenti supportano Crittografia disco di Azure:

| Distribuzione di Linux | Versione | Tipo di volume supportato per la crittografia|
| --- | --- |--- |
| Ubuntu | 18,04| Disco del sistema operativo e dati |
| Ubuntu | 16.04| Disco del sistema operativo e dati |
| Ubuntu | 14.04.5</br>[con il kernel ottimizzato per Azure aggiornato alla versione 4.15 o successiva](disk-encryption-troubleshooting.md) | Disco del sistema operativo e dati |
| RHEL | 7.7 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 7.6 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 7.5 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 7.4 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 7.3 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 7.2 | Sistema operativo e disco dati (vedere la nota seguente) |
| RHEL | 6.8 | Disco dati (vedere la nota seguente) |
| RHEL | 6.7 | Disco dati (vedere la nota seguente) |
| CentOS | 7.7 | Disco del sistema operativo e dati |
| CentOS | 7.6 | Disco del sistema operativo e dati |
| CentOS | 7.5 | Disco del sistema operativo e dati |
| CentOS | 7.4 | Disco del sistema operativo e dati |
| CentOS | 7.3 | Disco del sistema operativo e dati |
| CentOS | 7.2n | Disco del sistema operativo e dati |
| CentOS | 6.8 | Disco dati |
| openSUSE | 42.3 | Disco dati |
| SLES | 12-SP4 | Disco dati |
| SLES | 12-SP3 | Disco dati |

> [!NOTE]
> La nuova implementazione di Crittografia disco di Azure è supportata per il sistema operativo RHEL e il disco dati per le immagini RHEL7 pay-as-You-Go.  
>
> ADE è supportato anche per RHEL Bring-Your-Own-Subscription Gold Images, ma solo **dopo** la registrazione dell'abbonamento. Per altre informazioni, vedere [Red Hat Enterprise Linux Linux Bring-Your-Own-Subscription Gold Images in AzureFor more information, see Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images in Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisiti aggiuntivi per le macchine virtuali

Crittografia disco di Azure richiede che i moduli dm-crypt e vfat siano presenti nel sistema. La rimozione o la disabilitazione di vfat dall'immagine predefinita impedirà al sistema di leggere il volume della chiave e di ottenere la chiave necessaria per sbloccare i dischi nei riavvii successivi. System hardening steps that remove the vfat module from the system are not compatible with Azure Disk Encryption. 

Prima di abilitare la crittografia, i dischi dati da crittografare devono essere correttamente elencati in /etc/fstab. Usare un nome del dispositivo a blocchi permanente per questa voce, in quanto i nomi di dispositivo nel formato "dev/sdX" non possono essere considerati affidabili per l'associazione con lo stesso disco tra un riavvio e l'altro, in particolare dopo l'applicazione della crittografia. Per altre informazioni su questo comportamento, vedere: [Risolvere il problema dei nomi di dispositivo nelle macchine virtuali Linux](troubleshoot-device-names-problems.md)

Verificare che le impostazioni /etc/fstab siano configurate correttamente per il montaggio. Per configurare queste impostazioni, eseguire il comando mount -a o riavviare la macchina virtuale e attivare il rimontaggio in questo modo. Al termine, controllare l'output del comando lsblk per verificare che l'unità desiderata sia ancora montata. 
- Se il file /etc/fstab non monta l'unità in modo corretto prima di abilitare la crittografia; Crittografia dischi di Azure non sarà in grado di montarla correttamente.
- Il processo di Crittografia dischi di Azure sposterà le informazioni di montaggio da /etc/fstab e le inserirà in un proprio file di configurazione come parte del processo di crittografia. Non allarmarsi per la mancanza della voce da /etc/fstab dopo il completamento della crittografia dell'unità dati.
- Prima di avviare la crittografia, assicurarsi di arrestare tutti i servizi e i processi che potrebbero scrivere su dischi dati montati e disabilitarli, in modo che non vengano riavviati automaticamente dopo un riavvio. Questi potrebbero mantenere i file aperti su queste partizioni, impedendo la procedura di crittografia per rimontarli, causando un errore di crittografia. 
- Dopo il riavvio, il processo di Crittografia dischi di Azure avrà bisogno di tempo per montare i dischi appena crittografati. Non saranno disponibili immediatamente dopo un riavvio. Il processo richiede tempo per iniziare, sbloccare e quindi montare le unità crittografate prima che siano disponibili per l'accesso da parte di altri processi. Questo processo potrebbe richiedere più di un minuto dopo il riavvio, a seconda delle caratteristiche di sistema.

Un esempio di comandi che possono essere usati per montare i dischi dati e creare le voci /etc/fstab necessarie è disponibile nello [script dell'interfaccia](https://github.com/ejarvi/ade-cli-getting-started) della riga di comando dei prerequisiti di Crittografia disco di Azure (righe 244-248) e nello script di [PowerShell dei prerequisiti](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)di Crittografia disco di Azure. 

## <a name="networking-requirements"></a>Requisiti di rete

Per abilitare la funzionalità crittografia dei dischi di Azure, le macchine virtuali Linux devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:To enable the Azure Disk Encryption feature, the Linux VMs must meet the following network endpoint configuration requirements:
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, \[\]la macchina virtuale Linux deve essere in grado di connettersi a un endpoint di Azure Active Directory login.microsoftonline.com .
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, la macchina virtuale Linux deve essere in grado di connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La macchina virtuale Linux deve essere in grado di connettersi a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements  

Crittografia disco di Azure richiede un insieme di credenziali delle chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.Your key vault and VMs must reside in the same Azure region and subscription.

Per informazioni dettagliate, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure.](disk-encryption-key-vault.md)

## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione relativa alla crittografia del disco di Azure:The following table defines some of the common terms used in Azure disk encryption documentation:

| Terminologia | Definizione |
| --- | --- |
| Insieme di credenziali chiave di Azure | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione dell'insieme di credenziali delle chiavi di Azure e Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information, see the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentation and Creating and [configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md). |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) è il sottosistema di crittografia del disco trasparente e basato su Linux usato per abilitare la crittografia del disco nelle macchine virtuali Linux.DM-Crypt is the Linux-based, transparent disk-encryption subsystem that's used to enable disk encryption on Linux VMs. |
| Chiave di crittografia chiave (KEK) | Chiave asimmetrica (RSA 2048) che è possibile utilizzare per proteggere o racchiudere il segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione dell'insieme di credenziali delle chiavi di Azure e Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information, see the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentation and Creating and [configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva - Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di AzureQuickstart - Create and encrypt a Linux VM with Azure CLI](disk-encryption-cli-quickstart.md)
- [Guida introduttiva - Creare e crittografare una macchina virtuale Linux con Azure PowershellQuickstart - Create and encrypt a Linux VM with Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-linux.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per i prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](disk-encryption-key-vault.md)


