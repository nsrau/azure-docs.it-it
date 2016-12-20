---
title: Glossario di Azure - Dizionario di Azure | Documentazione Microsoft
description: Usare il glossario di Azure per comprendere la terminologia cloud della piattaforma Azure. Questo breve dizionario di Azure include le definizioni dei termini comuni relativi al cloud usati in Azure.
keywords: Dizionario di Azure, terminologia cloud, glossario di Azure, definizioni terminologiche, termini del cloud
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: 
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: monicar
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: bcfa97c08cb55aba1ab69e67843abb81805a8884


---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossario di Microsoft Azure: un dizionario di terminologia cloud sulla piattaforma Azure
Il glossario di Microsoft Azure è un breve dizionario di terminologia cloud per la piattaforma Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Ricercare le definizioni dei servizi e altri termini relativi al cloud
* Per le definizioni dei servizi di Azure e le relative controparti AWS, vedere [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).
* Per la terminologia generale di settore relativa al cloud, vedere [Terminologia del cloud computing](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Il glossario di Azure con i due riferimenti indicati sopra offre una tassonomia end-to-end per Azure e il settore del cloud.  

## <a name="azure-glossary-list"></a>Elenco del glossario di Azure
### <a name="a-nameaccountaaccount"></a><a name="account"></a>account
Account Microsoft aziendale, dell'istituto di istruzione o personale usato per l'accesso e la gestione di una sottoscrizione di Azure.  
Vedere anche [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="a-nameavailability-setaavailability-set"></a><a name="availability-set"></a>set di disponibilità
Raccolta di macchine virtuali gestite insieme per offrire ridondanza delle applicazioni e affidabilità. Durante un evento di manutenzione pianificato o non pianificato, l'uso di un set di disponibilità assicura la disponibilità di almeno una macchina virtuale.  
Vedere anche [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="a-nameclassic-modelaazure-classic-deployment-model"></a><a name="classic-model"></a>modello di distribuzione classica di Azure
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) usati per la distribuzione delle risorse in Azure. Il nuovo modello è Azure Resource Manager. Alcune risorse di Azure possono essere distribuite con uno dei due modelli, mentre altre possono essere distribuite con entrambi i modelli. Il materiale sussidiario delle singole risorse di Azure offre informazioni dettagliate sul modello o sui modelli con cui è possibile distribuire una risorsa.

### <a name="a-namecliaazure-command-line-interface-cli"></a><a name="cli"></a>interfaccia della riga di comando di Azure
[Interfaccia della riga di comando](xplat-cli-install.md) che può essere usata per la gestione dei servizi di Azure da PC Windows, OSX e Linux.

### <a name="a-namepowershellaazure-powershell"></a><a name="powershell"></a>Azure PowerShell
[Interfaccia della riga di comando](powershell-install-configure.md) per la gestione dei servizi di Azure tramite la riga di comando da PC Windows. Alcuni servizi o funzionalità dei servizi possono essere gestite solo tramite PowerShell o l'interfaccia della riga di comando. Il materiale sussidiario delle singole risorse di Azure offre informazioni dettagliate sul modello o sui modelli con cui è possibile distribuire una risorsa.   
Vedere anche [Come installare e configurare Azure PowerShell](powershell-install-configure.md)

### <a name="a-namearm-modelaazure-resource-manager-deployment-model"></a><a name="arm-model"></a>modello di distribuzione Azure Resource Manager
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) usati per la distribuzione delle risorse in Microsoft Azure. L'altro è il modello di distribuzione classica. Alcune risorse di Azure possono essere distribuite con uno dei due modelli, mentre altre possono essere distribuite con entrambi i modelli. Il materiale sussidiario delle singole risorse di Azure offre informazioni dettagliate sul modello o sui modelli con cui è possibile distribuire una risorsa.

### <a name="a-namefault-domainafault-domain"></a><a name="fault-domain"></a>Dominio di errore
Raccolta di macchine virtuali di un set di disponibilità nelle quali può verificarsi un errore contemporaneamente. Un esempio è un gruppo di macchine virtuali in un rack che condividono un'unità di alimentazione e un commutatore di rete comune. In Azure, le macchine virtuali di un set di disponibilità vengono suddivise automaticamente su più domini di errore.  
Vedere anche [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

### <a name="a-namegeoageo"></a><a name="geo"></a>area geografica
Limite definito per la residenza dei dati che contiene in genere due o più aree. I limiti possono essere compresi all'interno o superare i confini nazionali e sono influenzati dalla regolamentazione fiscale. Ogni area geografica include almeno un'area. Asia Pacifico e Giappone sono esempi di aree geografiche. Chiamata anche *geografia*.  
Vedere anche [Aree di Azure](best-practices-availability-paired-regions.md)

### <a name="a-namegeo-replicationageo-replication"></a><a name="geo-replication"></a>replica geografica
Processo di replica automatica del contenuto, ad esempio di BLOB, tabelle e code all'interno di una coppia di aree.  
Vedere anche [Replica geografica attiva per il database SQL di Azure](sql-database/sql-database-geo-replication-overview.md)

### <a name="a-nameimageaimage"></a><a name="image"></a>immagine
File che contiene il sistema operativo e la configurazione delle applicazioni che può essere usato per creare qualsiasi numero di macchine virtuali. In Azure sono disponibili due tipi di immagini: l'immagine della macchina virtuale e l'immagine del sistema operativo. Immagine di macchina virtuale include un sistema operativo e tutti i dischi collegati a una macchina virtuale quando viene creata l'immagine. L'immagine del sistema operativo contiene solo un sistema operativo generalizzato senza configurazioni del disco dati.  
Vedere anche [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="a-namelimitsalimits"></a><a name="limits"></a>limiti
Numero di risorse che è possibile creare o benchmark delle prestazioni che può essere raggiunto. I limiti sono in genere associati a sottoscrizioni, servizi e offerte.  
Vedere anche [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md)

### <a name="a-nameload-balanceraload-balancer"></a><a name="load-balancer"></a>bilanciamento del carico
Risorsa che distribuisce il traffico in ingresso nei computer di una rete. In Azure, il servizio di bilanciamento del carico distribuisce il traffico nelle macchine virtuali definite in un set di bilanciamento del carico . Il servizio di [bilanciamento del carico](load-balancer/load-balancer-overview.md) può essere connesso a Internet oppure interno.  

### <a name="a-nameofferaoffer"></a><a name="offer"></a>offerta
Prezzi, crediti e termini correlati applicabili a una sottoscrizione di Azure.  
Vedere la pagina [Dettagli delle offerte per Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/)

### <a name="a-nameportalaportal"></a><a name="portal"></a>portale
Portale Web sicuro usato per la distribuzione e la gestione dei servizi di Azure.  Sono disponibili due portali: il [portale di Azure](http://portal.azure.com/) e il [portale di Azure classico](http://manage.windowsazure.com/). Alcuni servizi sono disponibili in entrambi i portali, mentre altri sono disponibili solo in un portale o nell'altro. La pagina [Grafico della disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/) visualizza un elenco dei servizi disponibili in ogni portale.  

### <a name="a-nameregionaregion"></a><a name="region"></a>area
Area all'interno di un'area geografica che non supera i confini nazionali e include uno o più data center. I prezzi, i servizi regionali e i tipi di offerta sono resi disponibili a livello di area. Un'area viene in genere associata a un'altra area che può trovarsi a centinaia di chilometri di distanza in modo da formare una coppia di aree. Le coppie di aree possono essere usate come meccanismo per il ripristino di emergenza e gli scenari a disponibilità elevata. Chiamata anche genericamente *posizione*.  
Vedere anche [Aree di Azure](best-practices-availability-paired-regions.md)

### <a name="a-nameresourcearesource"></a><a name="resource"></a>risorsa
Elemento incluso nella soluzione Azure. Ogni servizio di Azure consente di distribuire diversi tipi di risorse, ad esempio database o macchine virtuali.   
Vedere anche [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

### <a name="a-nameresource-grouparesource-group"></a><a name="resource-group"></a>gruppo di risorse
Contenitore in Resource Manager che include le risorse correlate di un'applicazione. Il gruppo di risorse può includere tutte le risorse di un'applicazione o solo le risorse raggruppate logicamente. È possibile decidere come si desidera allocare le risorse a gruppi di risorse nel modo appropriato per l'organizzazione.  
Vedere anche [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

### <a name="a-namearm-templatearesource-manager-template"></a><a name="arm-template"></a>modello di Resource Manager
File JSON che definisce in modo dichiarativo una o più risorse di Azure e che definisce le dipendenze tra le risorse distribuite. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto.  
Vedere anche [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md)

### <a name="a-nameresource-provideraresource-provider"></a><a name="resource-provider"></a>provider di risorse
Servizio che fornisce le risorse da distribuire e gestire tramite Resource Manager. Ogni provider di risorse offre operazioni per l'uso delle risorse distribuite. È possibile accedere ai provider di risorse tramite il portale di Azure, Azure PowerShell e diversi SDK di programmazione.  
Vedere anche [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

### <a name="a-namerolearole"></a><a name="role"></a>ruolo
Mezzo per il controllo dell'accesso che può essere assegnato a utenti, gruppi e servizi. I ruoli sono in grado di eseguire azioni, ad esempio la creazione, la gestione e la lettura nelle risorse di Azure.  
Vedere anche [Controllo degli accessi in base al ruolo: ruoli predefiniti](active-directory/role-based-access-built-in-roles.md)

### <a name="a-nameslaaservice-level-agreement-sla"></a><a name="sla"></a>contratto di servizio
Contratto di servizio che definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Ogni servizio di Azure ha un contratto di servizio specifico.  
Vedere anche [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/)

### <a name="a-namestorage-accountastorage-account"></a><a name="storage-account"></a>account di archiviazione
Account di archiviazione che consente di accedere a BLOB di Azure e ai servizi di accodamento, tabelle e file in archiviazione di Azure. L'account di archiviazione offre uno spazio dei nomi univoco per gli oggetti dati dell'archiviazione di Azure.  
Vedere anche [Informazioni sugli account di archiviazione di Azure](storage/storage-create-storage-account.md)

### <a name="a-namesubscriptionasubscription"></a><a name="subscription"></a>sottoscrizione
Contratto tra il cliente e Microsoft che consente di ricevere i servizi di Azure. Le tariffe della sottoscrizione e i relativi termini dipendono dall'offerta scelta per la sottoscrizione. Vedere [Contratto di sottoscrizione Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Vedere anche [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

### <a name="a-nametagatag"></a><a name="tag"></a>tag
Termine di indicizzazione che consente di suddividere le risorse in categorie in base ai requisiti di gestione o fatturazione. È possibile usare i tag quando si ha un insieme complesso di gruppi di risorse e risorse ed è necessario visualizzare tali risorse nel modo più razionale. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto.  
Vedere anche [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md)

### <a name="a-nameupdate-domainaupdate-domain"></a><a name="update-domain"></a>dominio di aggiornamento
Raccolta di macchine virtuali di un set di disponibilità che vengono aggiornate contemporaneamente. Le macchine virtuali nello stesso dominio di aggiornamento vengono riavviate contemporaneamente durante la manutenzione pianificata. Azure non riavvia mai più di un dominio di aggiornamento alla volta.  
Vedere anche [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

### <a name="a-namevmavirtual-machine"></a><a name="vm"></a>macchina virtuale
Implementazione del software di un computer fisico che esegue un sistema operativo. È possibile eseguire più macchine virtuali contemporaneamente nello stesso hardware. In Azure sono disponibili macchine virtuali di dimensioni diverse.  
Vedere anche [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/)

### <a name="a-namevm-extensionavirtual-machine-extension"></a><a name="vm-extension"></a>estensione macchina virtuale
Risorsa che implementa comportamenti o funzionalità utili per il funzionamento di altri programmi o che consentono di interagire con un computer in esecuzione. È possibile ad esempio usare l'estensione di accesso della macchina virtuale per reimpostare o modificare i valori di accesso remoto in una macchina virtuale di Azure.  
Vedere anche [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali (Windows)](virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali (Linux)](virtual-machines/virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="a-namevnetavirtual-network"></a><a name="vnet"></a>rete virtuale
Rete che offre la connettività tra le risorse di Azure e che è isolata da tutti gli altri tenant di Azure. Può essere connessa ad altre reti virtuali di Azure tramite un [Gateway VPN di Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) e alla rete locale usando [più opzioni](vpn-gateway/vpn-gateway-plan-design.md). È possibile controllare completamente i blocchi di indirizzi IP, le impostazioni DNS, i criteri di sicurezza e le tabelle di route in questa rete.  
Vedere anche [Panoramica di Rete virtuale](virtual-network/virtual-networks-overview.md)  

### <a name="see-also"></a>**Vedere anche**
* [Inizia a usare Azure](https://azure.microsoft.com/get-started/)
* [Centro risorse cloud](https://azure.microsoft.com/resources/)  
* [Azure per le applicazioni aziendali](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure nel data center](https://azure.microsoft.com/overview/business-apps-on-azure/)




<!--HONumber=Nov16_HO3-->


