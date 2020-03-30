---
title: Domande frequenti sulle prestazioni di SMB per i file NetApp di Azure Documenti Microsoft
description: Risposte alle domande frequenti sulle prestazioni SMB per i file NetApp di Azure.Answers asked questions about SMB performance for Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460450"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Domande frequenti sulle prestazioni SMB per i file NetApp di Azure

Questo articolo risponde alle domande frequenti sulle procedure consigliate per le prestazioni SMB per i file NetApp di Azure.This article answers frequently asked questions (FAQs) about SMB performance best practices for Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB multicanale è abilitato nelle condivisioni SMB? 

Sì, SMB multicanale è abilitato per impostazione predefinita, una modifica messa in atto all'inizio di gennaio 2020. Tutte le condivisioni SMB precedenti ai volumi SMB hanno la funzionalità abilitata e la funzionalità verrà abilitata anche per tutti i volumi appena creati al momento della creazione. 

Qualsiasi connessione SMB stabilita prima dell'abilitazione della funzionalità dovrà essere reimpostata per sfruttare la funzionalità multicanale SMB. Per ripristinare, è possibile disconnettere e riconnettere la condivisione SMB.

## <a name="is-rss-supported"></a>RSS è supportato?

Sì, I file NetApp di Azure supportano il receive-side-scaling (RSS).

Con SMB multicanale abilitato, un client SMB3 stabilisce più connessioni TCP al server SMB File di Azure NetApp su una scheda di interfaccia di rete (NIC) che è singola RSS in grado. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quali versioni di Windows supportano SMB multicanale?

Windows supporta SMB multicanale da Windows 2012 per ottenere prestazioni ottimali.  Per informazioni dettagliate, vedere Distribuzione di [SMB multicanale](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [Nozioni di base su SMB multicanale.](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 


## <a name="does-my-azure-virtual-machine-support-rss"></a>La macchina virtuale di Azure supporta RSS?

Per verificare se le schede di interfaccia di `Get-SmbClientNetworkInterface` rete della macchina `RSS Capable`virtuale di Azure supportano RSS, eseguire il comando come indicato di seguito e controllare il campo : 

![Supporto RSS per la macchina virtuale di AzureRSS support for Azure virtual machine](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>I file di Azure NetApp supportano SMB Direct?

No, i file NetApp di Azure non supportano SMB diretto. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual è il vantaggio del multicanale SMB? 

La funzionalità Multicanale SMB consente a un client SMB3 di stabilire un pool di connessioni su una singola scheda di interfaccia di rete (NIC) o più schede di interfaccia di rete e di utilizzarle per inviare richieste per una singola sessione SMB. Al contrario, per impostazione specifica, SMB1 e SMB2 richiedono al client di stabilire una connessione e inviare tutto il traffico SMB per una determinata sessione su tale connessione. Questa singola connessione limita le prestazioni complessive del protocollo che possono essere ottenute da un singolo client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>È necessario configurare più schede di interfaccia di rete nel client per SMB?

No. Il client SMB corrisponderà al conteggio NIC restituito dal server SMB.  Ogni volume di archiviazione è accessibile da un solo endpoint di archiviazione.  Ciò significa che verrà utilizzata una sola scheda di interfaccia di rete per una determinata relazione SMB.  

Come illustrato `Get-SmbClientNetworkInterace` nell'output di seguito, la macchina virtuale ha due interfacce di rete: 15 e 12.As the output of below shows, the virtual machine has two network interfaces - 15 and 12.  Come illustrato di `Get-SmbMultichannelConnection`seguito sotto il comando , anche se sono presenti due NICS che supportano RSS, viene utilizzata solo l'interfaccia 12 in relazione alla condivisione SMB; l'interfaccia 15 non è in uso.

![NICS con supporto RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Il gruppo NIC è supportato in Azure?

NIC Teaming is not supported in Azure. Sebbene nelle macchine virtuali di Azure siano supportate più interfacce di rete, rappresentano un costrutto logico anziché fisico. Di conseguenza, non forniscono alcuna tolleranza di errore.  Inoltre, la larghezza di banda disponibile per una macchina virtuale di Azure viene calcolata per la macchina stessa e non per qualsiasi singola interfaccia di rete.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Com'è la performance per SMB multicanale?

I test e i grafici seguenti dimostrano la potenza del multicanale SMB nei carichi di lavoro a istanza singola.

### <a name="random-io"></a>I/O casuale  

Con SMB multicanale disabilitato sul client, i test di lettura e scrittura pura da 8 KiB sono stati eseguiti utilizzando FIO e un working set da 40-GiB.  La condivisione SMB è stata scollegata tra ogni test, con incrementi `1`del`4``8`numero`16`di connessioni client SMB per le impostazioni dell'interfaccia di rete RSS di , , , . `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` I test dimostrano che `4` l'impostazione predefinita di è sufficiente per i carichi di lavoro con utilizzo intensivo di I/O. incrementando `8` `16` e non ha avuto alcun effetto. 

Il `netstat -na | findstr 445` comando ha dimostrato che sono `1` state `4` `8` stabilite `16`connessioni aggiuntive con incrementi da a e a .  Quattro core CPU sono stati completamente utilizzati per SMB durante ogni `Per Processor Network Activity Cycles` test, come confermato dalla statistica perfmon (non incluso in questo articolo).)

![Test i/o casuali](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

La macchina virtuale di Azure non influisce sui limiti di Archiviazione SMB (né NFS).  Come illustrato di seguito, il tipo di istanza D16 ha una velocità limitata di 32.000 per le operazioni di I/O al secondo di archiviazione memorizzate nella cache e 25.600 per le operazioni di I/O al secondo di archiviazione non memorizzate nella cache.  Tuttavia, il grafico precedente mostra un numero significativamente maggiore di I/O rispetto a SMB.

![Confronto I/O casuale](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>IO sequenziale 

Test simili ai test di I/O casuali descritti in precedenza sono stati eseguiti con I/O sequenziali da 64 KiB. Anche se gli aumenti nel numero di connessioni client per interfaccia di rete RSS oltre 4' non hanno avuto alcun effetto evidente sull'I/O casuale, lo stesso non si applica all'I/O sequenziale. Come illustrato nel grafico seguente, ogni aumento è associato a un corrispondente aumento della velocità effettiva di lettura. La velocità effettiva di scrittura è rimasta piatta a causa delle restrizioni della larghezza di banda di rete poste da Azure per ogni tipo/dimensione dell'istanza. 

![Test I/O sequenziali](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure impone limiti di frequenza di rete per ogni tipo/dimensione di macchina virtuale. Il limite di velocità viene imposto solo al traffico in uscita. Il numero di schede di interfaccia di rete presenti in una macchina virtuale non influisce sulla quantità totale di larghezza di banda disponibile per la macchina.  Ad esempio, il tipo di istanza D16 ha un limite di rete imposto di 8000 Mbps (1.000 MiB/s).  Come illustrato nel grafico sequenziale precedente, il limite influisce sul traffico in uscita (scritture) ma non sulle letture multicanale.

![Confronto I/O sequenziale](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>È consigliata la rete accelerata?

Per ottenere le massime prestazioni, è consigliabile configurare [Accelerated Networking,](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) se possibile. Tenere presenti le considerazioni seguenti:Keep the following considerations in mind:  

* Il portale di Azure abilita la rete accelerata per impostazione predefinita per le macchine virtuali che supportano questa funzionalità.  Tuttavia, altri metodi di distribuzione, ad esempio Ansible e strumenti di configurazione simili potrebbero non.  La mancata abilitazione della rete accelerata può migliorare le prestazioni di un computer.  
* Se Accelerated Networking non è abilitato sull'interfaccia di rete di una macchina virtuale a causa della mancanza di supporto per un tipo di istanza o le dimensioni, rimarrà disabilitata con tipi di istanza più grandi. In questi casi è necessario un intervento manuale.

## <a name="are-jumbo-frames-supported"></a>Sono supportati i frame jumbo?

I frame Jumbo non sono supportati con le macchine virtuali di Azure.Jumbo frames are not supported with Azure virtual machines.

## <a name="is-smb-signing-supported"></a>La firma SMB è supportata? 

Il protocollo SMB fornisce la base per la condivisione di file e stampanti e altre operazioni di rete, ad esempio l'amministrazione remota di Windows. Per impedire gli attacchi man in the middle che modificano i pacchetti SMB durante la trasmissione, il protocollo SMB supporta la firma digitale dei pacchetti SMB. 

SMB Signing is supported for all SMB protocol versions that are supported by Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual è l'impatto sulle prestazioni della firma SMB?  

La firma SMB ha un effetto deleterio sulle prestazioni di SMB. Tra le altre possibili cause della riduzione delle prestazioni, la firma digitale di ogni pacchetto utilizza una CPU aggiuntiva sul lato client, come illustrato nell'output di perfmon riportato di seguito. In questo caso, Core 0 viene visualizzato responsabile per SMB, inclusa la firma SMB.  Un confronto con i numeri di velocità effettiva di lettura sequenziale non multicanale nella sezione precedente mostra che la firma SMB riduce la velocità effettiva complessiva da 875MiB/s a circa 250 MiB/s. 

![Impatto sulle prestazioni della firma SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti sui file NetApp di AzureFAQQs About Azure NetApp Files](azure-netapp-files-faqs.md)
- Vedere [File di Azure NetApp: Condivisioni di file Enterprise gestite per carichi](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) di lavoro SMB sull'uso delle condivisioni file SMB con i file di Azure NetApp.See the Azure NetApp Files: Managed Enterprise File Shares for SMB Workloads about using SMB file shares with Azure NetApp Files.
