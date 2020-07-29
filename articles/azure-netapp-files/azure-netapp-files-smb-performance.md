---
title: Domande frequenti sulle prestazioni SMB per Azure NetApp Files | Microsoft Docs
description: Risposte alle domande frequenti sulle prestazioni SMB per Azure NetApp Files.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460450"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Domande frequenti sulle prestazioni SMB per Azure NetApp Files

Questo articolo risponde alle domande frequenti sulle procedure consigliate per le prestazioni SMB per Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB multicanale è abilitato nelle condivisioni SMB? 

Sì, SMB multicanale è abilitato per impostazione predefinita, una modifica apportata nel primo gennaio 2020. Per tutte le condivisioni SMB che prevedono volumi SMB esistenti è stata abilitata la funzionalità e per tutti i volumi appena creati sarà abilitata anche la funzionalità al momento della creazione. 

Qualsiasi connessione SMB stabilita prima dell'abilitazione della funzionalità dovrà essere reimpostata per sfruttare la funzionalità SMB multicanale. Per reimpostare, è possibile disconnettere e riconnettere la condivisione SMB.

## <a name="is-rss-supported"></a>RSS è supportato?

Sì, Azure NetApp Files supporta Receive-Side-Scaling (RSS).

Con SMB multicanale abilitato, un client SMB3 stabilisce più connessioni TCP al server Azure NetApp Files SMB tramite una scheda di interfaccia di rete (NIC) che supporta solo RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quali versioni di Windows supportano SMB multicanale?

Windows supporta SMB multicanale a partire da Windows 2012 per garantire prestazioni ottimali.  Per informazioni dettagliate, vedere [distribuire SMB multicanale](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [le nozioni di base di SMB multicanale](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>La macchina virtuale di Azure supporta RSS?

Per verificare se le schede NIC delle macchine virtuali di Azure supportano RSS, eseguire il comando `Get-SmbClientNetworkInterface` come indicato di seguito e selezionare il campo `RSS Capable` : 

![Supporto RSS per la macchina virtuale di Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files supporta SMB diretto?

No, Azure NetApp Files non supporta SMB diretto. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual è il vantaggio di SMB multicanale? 

La funzionalità SMB multicanale consente a un client SMB3 di stabilire un pool di connessioni su una singola scheda di interfaccia di rete (NIC) o su più schede di interfaccia di rete e di utilizzarle per inviare richieste per una singola sessione SMB. Al contrario, per progettazione, SMB1 e SMB2 è necessario che il client stabilisca una connessione e invii tutto il traffico SMB per una determinata sessione tramite tale connessione. Questa singola connessione limita le prestazioni complessive del protocollo che è possibile ottenere da un singolo client.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>È necessario configurare più schede di rete nel client per SMB?

No. Il client SMB corrisponderà al numero di NIC restituito dal server SMB.  Ogni volume di archiviazione è accessibile da un solo endpoint di archiviazione.  Ciò significa che verrà usata una sola scheda di interfaccia di rete per ogni relazione SMB specificata.  

Come illustrato nell'output di `Get-SmbClientNetworkInterace` seguito, la macchina virtuale ha due interfacce di rete, ovvero 15 e 12.  Come illustrato di seguito sotto il comando `Get-SmbMultichannelConnection` , anche se sono presenti due schede di interfaccia di rete che supportano RSS, viene usata solo l'interfaccia 12 in connessione alla condivisione SMB; l'interfaccia 15 non è in uso.

![NIC compatibili con RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Il gruppo NIC è supportato in Azure?

Gruppo NIC non supportato in Azure. Sebbene siano supportate più interfacce di rete nelle macchine virtuali di Azure, rappresentano un costrutto logico anziché un costrutto fisico. Di conseguenza, non forniscono tolleranza di errore.  Inoltre, la larghezza di banda disponibile per una macchina virtuale di Azure viene calcolata per il computer stesso e non per le singole interfacce di rete.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Quali sono le prestazioni, ad esempio SMB multicanale?

I test e i grafici seguenti illustrano la potenza di SMB multicanale sui carichi di lavoro a istanza singola.

### <a name="random-io"></a>I/O casuale  

Con SMB multicanale disabilitato nel client, i test di lettura e scrittura puri da 8 KB sono stati eseguiti con FIO e 40-GiB working set.  La condivisione SMB è stata scollegata tra ogni test, con incrementi del numero di connessioni client SMB per ogni impostazione dell'interfaccia di rete RSS di `1` , `4` , `8` , `16` , `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . I test indicano che l'impostazione predefinita di `4` è sufficiente per i carichi di lavoro con utilizzo intensivo di I/O; l'incremento a `8` e `16` non ha effetto. 

Il comando ha `netstat -na | findstr 445` dimostrato che sono state stabilite connessioni aggiuntive con incrementi da `1` a a e a `4` `8` `16` .  Quattro core CPU sono stati completamente utilizzati per SMB durante ogni test, come confermato dalla `Per Processor Network Activity Cycles` statistica PerfMon (non incluso in questo articolo).

![Test di I/O casuali](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

La macchina virtuale di Azure non influisce sui limiti di I/O di archiviazione SMB (o NFS).  Come illustrato di seguito, il tipo di istanza di D16 ha una frequenza limitata di 32.000 per gli IOPS di archiviazione memorizzati nella cache e 25.600 per gli IOPS di archiviazione non memorizzati nella cache.  Tuttavia, il grafico precedente mostra un numero significativamente maggiore di I/O su SMB.

![Confronto I/O casuale](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>IO sequenziale 

I test simili ai test di I/O casuali descritti in precedenza sono stati eseguiti con l'I/O sequenziale 64-KiB. Sebbene gli aumenti nel conteggio delle connessioni client per ogni interfaccia di rete RSS oltre il 4' non abbiano effetto evidente sull'I/O casuale, lo stesso non si applica all'I/O sequenziale. Come illustrato nel grafico seguente, ogni aumento è associato a un aumento corrispondente della velocità effettiva di lettura. La velocità effettiva di scrittura è rimasta piana a causa di restrizioni della larghezza di banda di rete posizionate da Azure per ogni tipo di istanza 

![Test di I/O sequenziali](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure pone limiti di velocità di rete per ogni tipo di macchina virtuale/dimensioni. Il limite di frequenza viene imposto solo sul traffico in uscita. Il numero di schede di rete presenti in una macchina virtuale non ha alcun impatto sulla quantità totale di larghezza di banda disponibile per il computer.  Il tipo di istanza D16, ad esempio, ha un limite di rete imposto di 8000 Mbps (1.000 MiB/s).  Come illustrato nel grafico sequenziale precedente, il limite influiscono sul traffico in uscita (scritture) ma non sulle letture multicanale.

![Confronto I/O sequenziale](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>La rete accelerata è consigliata?

Per ottenere prestazioni ottimali, è consigliabile configurare la [rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) laddove possibile. Tenere presenti le considerazioni seguenti:  

* Il portale di Azure Abilita la rete accelerata per impostazione predefinita per le macchine virtuali che supportano questa funzionalità.  Tuttavia, potrebbero non essere disponibili altri metodi di distribuzione, ad esempio Ansible e strumenti di configurazione analoghi.  La mancata abilitazione della rete accelerata può ostacolare le prestazioni di un computer.  
* Se la rete accelerata non è abilitata nell'interfaccia di rete di una macchina virtuale a causa della mancanza di supporto per un tipo di istanza o una dimensione, rimarrà disabilitata con i tipi di istanza più grandi. In questi casi sarà necessario un intervento manuale.

## <a name="are-jumbo-frames-supported"></a>Sono supportati i frame Jumbo?

I frame Jumbo non sono supportati con macchine virtuali di Azure.

## <a name="is-smb-signing-supported"></a>La firma SMB è supportata? 

Il protocollo SMB costituisce la base per la condivisione di file e stampanti e altre operazioni di rete, ad esempio l'amministrazione remota di Windows. Per impedire gli attacchi man in the middle che modificano i pacchetti SMB durante la trasmissione, il protocollo SMB supporta la firma digitale dei pacchetti SMB. 

La firma SMB è supportata per tutte le versioni del protocollo SMB supportate da Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual è l'effetto sulle prestazioni della firma SMB?  

La firma SMB ha un effetto deleterio sulle prestazioni SMB. Tra le altre possibili cause del calo delle prestazioni, la firma digitale di ogni pacchetto utilizza una CPU aggiuntiva sul lato client, come illustrato nell'output di PerfMon riportato di seguito. In questo caso, Core 0 viene visualizzato come responsabile di SMB, inclusa la firma SMB.  Un confronto con i numeri di velocità effettiva di lettura sequenziale non multicanale nella sezione precedente mostra che la firma SMB riduce la velocità effettiva complessiva da 875MiB/s a circa 250MiB/s. 

![Effetti sulle prestazioni della firma SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti sulla Azure NetApp Files](azure-netapp-files-faqs.md)
- Per i carichi di lavoro SMB sull'uso delle condivisioni file SMB con Azure NetApp Files, vedere [Azure NetApp Files: condivisioni file aziendali gestite](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) .
