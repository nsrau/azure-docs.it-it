---
title: Abilitare crittografia dischi di Azure per macchine virtuali Linux
description: Questo articolo fornisce istruzioni su come abilitare la crittografia del disco Microsoft Azure per le macchine virtuali Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 053c52d7d1a0282d72ad76408b77c96aa3b0e3e4
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174696"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Crittografia dischi di Azure per macchine virtuali Linux 

Crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Usa la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con [Azure Key Vault](../../key-vault/index.yml) per facilitare il controllo e la gestione delle chiavi e dei segreti di crittografia del disco. 

Se si usa il [Centro sicurezza di Azure](../../security-center/index.yml), l'utente viene avvisato se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se in precedenza è stato usato crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere [crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-overview-aad.md) . 
> - Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione. Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.

È possibile apprendere le nozioni di base di crittografia dischi di Azure per Linux in pochi minuti con la Guida [introduttiva creare e crittografare una VM Linux con l'interfaccia](disk-encryption-cli-quickstart.md) della riga di comando di Azure o [creare e crittografare una VM Linux con Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Macchine virtuali e sistemi operativi supportati

### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

Le macchine virtuali Linux sono disponibili in un [intervallo di dimensioni](sizes.md). Crittografia dischi di Azure non è disponibile nelle [VM Basic, serie A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)o nelle macchine virtuali che non soddisfano i requisiti di memoria minimi seguenti:

| Macchina virtuale | Requisito memoria minima |
|--|--|
| VM Linux quando si crittografano solo i volumi di dati| 2 GB |
| VM Linux durante la crittografia di volumi di dati e sistemi operativi e in cui la radice (/) file system utilizzo è 4 GB o meno | 8 GB |
| Macchine virtuali Linux durante la crittografia di volumi di dati e sistemi operativi e in cui la radice (/) file system utilizzo è maggiore di 4 GB | Utilizzo file system radice * 2. Ad esempio, i 16 GB di utilizzo file system radice richiedono almeno 32GB di RAM |

Una volta completato il processo di crittografia del disco del sistema operativo nelle macchine virtuali Linux, è possibile configurare la macchina virtuale in modo che venga eseguita con meno memoria. 

Crittografia dischi di Azure è disponibile anche per le macchine virtuali con archiviazione Premium. 

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Crittografia dischi di Azure è supportata in un subset di [distribuzioni Linux approvate per Azure](endorsed-distros.md), che è a sua volta un subset di tutte le distribuzioni di server Linux possibili.

![Diagramma di Venn di distribuzioni di server Linux che supportano crittografia dischi di Azure](./media/disk-encryption/ade-supported-distros.png)

Le distribuzioni di server Linux che non sono approvate da Azure non supportano crittografia dischi di Azure. di quelle approvate, solo le distribuzioni e le versioni seguenti supportano crittografia dischi di Azure:

| Distribuzione Linux | Versione | Tipo di volume supportato per la crittografia|
| --- | --- |--- |
| Ubuntu | 18,04| Disco del sistema operativo e dati |
| Ubuntu | 16.04| Disco del sistema operativo e dati |
| Ubuntu | 14.04.5</br>[con il kernel ottimizzato per Azure aggiornato alla versione 4.15 o successiva](disk-encryption-troubleshooting.md) | Disco del sistema operativo e dati |
| RHEL | 7,7 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 7.6 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 7.5 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 7.4 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 7.3 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 7.2 | Sistema operativo e disco dati (vedere la nota di seguito) |
| RHEL | 6.8 | Disco dati (vedere la nota di seguito) |
| RHEL | 6.7 | Disco dati (vedere la nota di seguito) |
| CentOS | 7,7 | Disco del sistema operativo e dati |
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
> La nuova implementazione di ADE è supportata per il sistema operativo RHEL e il disco dati per le immagini con pagamento in base al consumo di RHEL7. Crittografia dischi di Azure non è attualmente supportata per le immagini RHEL di tipo BYOS (Bring-Your-Own-Subscription). 

## <a name="additional-vm-requirements"></a>Requisiti aggiuntivi per le macchine virtuali

Crittografia dischi di Azure richiede che i moduli dm-crypt e VFAT siano presenti nel sistema. La rimozione o la disabilitazione di VFAT dall'immagine predefinita impedisce al sistema di leggere il volume della chiave e ottenere la chiave necessaria per sbloccare i dischi durante i successivi riavvii. I passaggi per la protezione avanzata del sistema che rimuovono il modulo VFAT dal sistema non sono compatibili con crittografia dischi di Azure. 

Prima di abilitare la crittografia, i dischi dati da crittografare devono essere elencati correttamente in/etc/fstab. Usare un nome del dispositivo a blocchi permanente per questa voce, in quanto i nomi di dispositivo nel formato "dev/sdX" non possono essere considerati affidabili per l'associazione con lo stesso disco tra un riavvio e l'altro, in particolare dopo l'applicazione della crittografia. Per altre informazioni su questo comportamento, vedere: [Risolvere il problema dei nomi di dispositivo nelle macchine virtuali Linux](troubleshoot-device-names-problems.md)

Verificare che le impostazioni /etc/fstab siano configurate correttamente per il montaggio. Per configurare queste impostazioni, eseguire il comando mount -a o riavviare la macchina virtuale e attivare il rimontaggio in questo modo. Al termine, controllare l'output del comando lsblk per verificare che l'unità desiderata sia ancora montata. 
- Se il file /etc/fstab non monta l'unità in modo corretto prima di abilitare la crittografia; Crittografia dischi di Azure non sarà in grado di montarla correttamente.
- Il processo di Crittografia dischi di Azure sposterà le informazioni di montaggio da /etc/fstab e le inserirà in un proprio file di configurazione come parte del processo di crittografia. Non allarmarsi per la mancanza della voce da /etc/fstab dopo il completamento della crittografia dell'unità dati.
- Prima di avviare la crittografia, assicurarsi di arrestare tutti i servizi e i processi che potrebbero scrivere sui dischi dati montati e disabilitarli, in modo che non vengano riavviati automaticamente dopo un riavvio. È possibile che i file vengano aperti in queste partizioni evitando che la procedura di crittografia li rimonta, causando un errore di crittografia. 
- Dopo il riavvio, il processo di Crittografia dischi di Azure avrà bisogno di tempo per montare i dischi appena crittografati. Non saranno disponibili immediatamente dopo un riavvio. Il processo richiede tempo per iniziare, sbloccare e quindi montare le unità crittografate prima che siano disponibili per l'accesso da parte di altri processi. Questo processo potrebbe richiedere più di un minuto dopo il riavvio, a seconda delle caratteristiche di sistema.

Un esempio di comandi che possono essere usati per montare i dischi dati e creare le voci/etc/fstab necessarie è disponibile nello script dell'interfaccia della riga di comando dei [prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started) (righe 244-248) e nei prerequisiti di [crittografia dischi di Azure PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Requisiti di rete

Per abilitare la funzionalità Crittografia dischi di Azure, è necessario che le macchine virtuali Linux soddisfino i requisiti di configurazione degli endpoint di rete seguenti:
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, la VM Linux deve essere in grado di connettersi a un endpoint Azure Active Directory, @no__t -0login. microsoftonline. com @ no__t-1.
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, la VM Linux deve essere in grado di connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM Linux deve essere in grado di connettersi a un endpoint di archiviazione di Azure che ospita il repository di estensioni di Azure e un account di archiviazione di Azure che ospita i file VHD.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia  

Crittografia dischi di Azure richiede un Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono trovarsi nella stessa area e nella stessa sottoscrizione di Azure.

Per informazioni dettagliate, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione di crittografia dischi di Azure:

| Terminologia | Definizione |
| --- | --- |
| Azure Key Vault | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) è il sottosistema di crittografia dei dischi trasparente basato su Linux usato per abilitare la crittografia dei dischi nelle macchine virtuali Linux. |
| Chiave di crittografia della chiave (KEK) | Chiave asimmetrica (RSA 2048) che è possibile usare per proteggere o eseguire il wrapping del segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: creare e crittografare una VM Linux con l'interfaccia della riga di comando](disk-encryption-cli-quickstart.md)
- [Guida introduttiva: creare e crittografare una VM Linux con Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scenari di crittografia dischi di Azure nelle macchine virtuali Linux](disk-encryption-linux.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md)


