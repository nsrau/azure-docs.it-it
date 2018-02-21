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
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: cbc4b8cdb0ff9255d0be02b998e67686921921ea
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2017
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossario di Microsoft Azure: un dizionario di terminologia cloud sulla piattaforma Azure

Il glossario di Microsoft Azure è un breve dizionario di terminologia cloud per la piattaforma Azure. Vedere anche la pagina relativa alla

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Definizioni dei servizi di Azure e delle rispettive controparti AWS.<!-- I propose to link to https://azure.microsoft.com/en-us/services/ instead of this -->
* [Terminologia del cloud computing](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - Terminologia generale relativa al cloud.

## <a name="account"></a>account
Account usato per accedere alla sottoscrizione di Azure e gestirla. Viene spesso definito account Azure anche se un account può essere un account Microsoft aziendale, dell'istituto di istruzione o personale esistente oppure un nome utente e una password di Office 365. È anche possibile creare un account per gestire una sottoscrizione di Azure quando ci si iscrive alla [versione di valutazione gratuita](https://azure.microsoft.com).  
Vedere [Iscriversi a una sottoscrizione di Azure con il proprio account di Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) e [Account che si possono usare per l'accesso](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>App per le API
Nome alternativo per [app del servizio app](#app-service-app).

## <a name="app-service-app"></a>app del servizio app
Risorse di calcolo fornite da [Servizio app di Azure](app-service/app-service-web-overview.md) per l'hosting di siti Web o applicazioni Web, API Web o [back-end per app per dispositivi mobili](app-service-mobile/app-service-mobile-value-prop.md). Le app del servizio app vengono chiamate anche *servizi app*, *app Web*, *app per le API* e *app per dispositivi mobili*.

## <a name="availability-set"></a>set di disponibilità
Raccolta di macchine virtuali gestite insieme per offrire ridondanza delle applicazioni e affidabilità. Durante un evento di manutenzione pianificato o non pianificato, l'uso di un set di disponibilità assicura la disponibilità di almeno una macchina virtuale.  
Vedere [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>modello di distribuzione classica di Azure
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) usati per la distribuzione delle risorse in Azure. Il nuovo modello è Azure Resource Manager. Alcuni servizi di Azure supportano solo il modello di sviluppo di Resource Manager, alcuni supportano solo il modello di sviluppo classico e alcuni supportano entrambi. La documentazione per ogni servizio di Azure specifica i modelli supportati.

## <a name="cli"></a>interfaccia della riga di comando di Azure
Interfaccia della riga di comando che può essere usata per la gestione dei servizi di Azure da PC Windows, OSX e Linux.  Alcuni servizi o funzionalità dei servizi possono essere gestite solo tramite PowerShell o l'interfaccia della riga di comando. Vedere [Interfaccia della riga di comando di Azure 2.0](/cli/azure/overview)

## <a name="powershell"></a>Azure PowerShell
Interfaccia della riga di comando per la gestione dei servizi di Azure tramite la riga di comando da PC Windows. Alcuni servizi o funzionalità dei servizi possono essere gestite solo tramite PowerShell o l'interfaccia della riga di comando.
Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>modello di distribuzione Azure Resource Manager
Uno dei due [modelli di distribuzione](resource-manager-deployment-model.md) usati per la distribuzione delle risorse in Microsoft Azure. L'altro è il modello di distribuzione classica. Alcuni servizi di Azure supportano solo il modello di sviluppo di Resource Manager, alcuni supportano solo il modello di sviluppo classico e alcuni supportano entrambi. La documentazione per ogni servizio di Azure specifica i modelli supportati.

## <a name="fault-domain"></a>dominio di errore
Raccolta di macchine virtuali di un set di disponibilità nelle quali può verificarsi un errore contemporaneamente. Un esempio è un gruppo di macchine virtuali in un rack che condividono un'unità di alimentazione e un commutatore di rete comune. In Azure, le macchine virtuali di un set di disponibilità vengono suddivise automaticamente su più domini di errore.  
Vedere [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>area geografica
Limite definito per la residenza dei dati che contiene in genere due o più aree. I limiti possono essere compresi all'interno o superare i confini nazionali e sono influenzati dalla regolamentazione fiscale. Ogni area geografica include almeno un'area. Asia Pacifico e Giappone sono esempi di aree geografiche. Chiamata anche *geografia*.  
Vedere [Aree di Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replica geografica
Processo di replica automatica del contenuto, ad esempio di BLOB, tabelle e code all'interno di una coppia di aree.  
Vedere [Replica geografica attiva per il database SQL di Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>immagine
File che contiene il sistema operativo e la configurazione delle applicazioni che può essere usato per creare qualsiasi numero di macchine virtuali. In Azure sono disponibili due tipi di immagini: l'immagine della macchina virtuale e l'immagine del sistema operativo. Immagine di macchina virtuale include un sistema operativo e tutti i dischi collegati a una macchina virtuale quando viene creata l'immagine. L'immagine del sistema operativo contiene solo un sistema operativo generalizzato senza configurazioni del disco dati.  
Vedere [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limiti
Numero di risorse che è possibile creare o benchmark delle prestazioni che può essere raggiunto. I limiti sono in genere associati a sottoscrizioni, servizi e offerte.  
Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>bilanciamento del carico
Risorsa che distribuisce il traffico in ingresso nei computer di una rete. In Azure, il servizio di bilanciamento del carico distribuisce il traffico nelle macchine virtuali definite in un set di bilanciamento del carico . Il servizio di [bilanciamento del carico](load-balancer/load-balancer-overview.md) può essere connesso a Internet oppure interno.  

## <a name="mobile-app"></a>app per dispositivi mobili
Nome alternativo per [app del servizio app](#app-service-app).

## <a name="offer"></a>offer
Prezzi, crediti e termini correlati applicabili a una sottoscrizione di Azure.  
Vedere la pagina [Dettagli delle offerte per Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
Portale Web sicuro usato per la distribuzione e la gestione dei servizi di Azure.

## <a name="region"></a>region
Area all'interno di un'area geografica che non supera i confini nazionali e include uno o più data center. I prezzi, i servizi regionali e i tipi di offerta sono resi disponibili a livello di area. Un'area viene in genere associata a un'altra area, che può trovarsi a centinaia di chilometri di distanza. Le coppie di aree possono essere usate come meccanismo per il ripristino di emergenza e gli scenari a disponibilità elevata. Chiamata anche genericamente *posizione*.  
Vedere [Aree di Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Elemento incluso nella soluzione Azure. Ogni servizio di Azure consente di distribuire diversi tipi di risorse, ad esempio database o macchine virtuali.   
Vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>gruppo di risorse
Contenitore in Resource Manager che include le risorse correlate di un'applicazione. Il gruppo di risorse può includere tutte le risorse di un'applicazione o solo le risorse raggruppate logicamente. È possibile decidere come si desidera allocare le risorse a gruppi di risorse nel modo appropriato per l'organizzazione.  
Vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>modello di Resource Manager
File JSON che definisce in modo dichiarativo una o più risorse di Azure e che definisce le dipendenze tra le risorse distribuite. Il modello può essere usato per distribuire le risorse in modo coerente e ripetuto.  
Vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>provider di risorse
Servizio che fornisce le risorse da distribuire e gestire tramite Resource Manager. Ogni provider di risorse offre operazioni per l'uso delle risorse distribuite. È possibile accedere ai provider di risorse tramite il portale di Azure, Azure PowerShell e diversi SDK di programmazione.  
Vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>role
Mezzo per il controllo dell'accesso che può essere assegnato a utenti, gruppi e servizi. I ruoli sono in grado di eseguire azioni, ad esempio la creazione, la gestione e la lettura nelle risorse di Azure.  
Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](active-directory/role-based-access-built-in-roles.md)

## <a name="sla"></a>contratto di servizio
Contratto di servizio che definisce gli impegni di Microsoft in merito a tempi di attività e connettività. Ogni servizio di Azure ha un contratto di servizio specifico.  
Vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>firma di accesso condiviso
Firma che consente di concedere accesso limitato a una risorsa, senza esporre la chiave dell'account. Ad esempio, [Archiviazione di Azure usa firme di accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) per concedere l'accesso client a oggetti come i BLOB. L'[hub IoT usa firme di accesso condiviso](iot-hub/iot-hub-devguide-security.md#security-tokens) per concedere ai dispositivi l'autorizzazione necessaria per inviare dati di telemetria.

## <a name="storage-account"></a>account di archiviazione
Account che consente di accedere a servizi BLOB, di accodamento, tabelle e file in Archiviazione di Azure. Il nome dell'account di archiviazione definisce uno spazio dei nomi univoco per gli oggetti dati di Archiviazione di Azure.  
Vedere [Informazioni sugli account di archiviazione di Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>sottoscrizione
Contratto tra il cliente e Microsoft che consente di ricevere i servizi di Azure. Le tariffe della sottoscrizione e i relativi termini dipendono dall'offerta scelta per la sottoscrizione.
Vedere [Contratto di Sottoscrizione Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) e [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Termine di indicizzazione che consente di suddividere le risorse in categorie in base ai requisiti di gestione o fatturazione. Quando è presente una raccolta complessa di risorse, è possibile usare tag per visualizzare gli asset nel modo più logico possibile. Ad esempio, è possibile contrassegnare le risorse che svolgono un ruolo simile nell'organizzazione o che appartengono allo stesso reparto.  
Vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>dominio di aggiornamento
Raccolta di macchine virtuali di un set di disponibilità che vengono aggiornate contemporaneamente. Le macchine virtuali nello stesso dominio di aggiornamento vengono riavviate contemporaneamente durante la manutenzione pianificata. Azure non riavvia mai più di un dominio di aggiornamento alla volta.  
Vedere [Gestire la disponibilità delle macchine virtuali Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Gestire la disponibilità delle macchine virtuali Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>macchina virtuale
Implementazione del software di un computer fisico che esegue un sistema operativo. È possibile eseguire più macchine virtuali contemporaneamente nello stesso hardware. In Azure sono disponibili macchine virtuali di dimensioni diverse.  
Vedere [Documentazione delle macchine virtuali](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>estensione macchina virtuale
Risorsa che implementa comportamenti o funzionalità utili per il funzionamento di altri programmi o che consentono di interagire con un computer in esecuzione. È possibile ad esempio usare l'estensione di accesso della macchina virtuale per reimpostare o modificare i valori di accesso remoto in una macchina virtuale di Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Vedere [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>rete virtuale
Rete che offre la connettività tra le risorse di Azure e che è isolata da tutti gli altri tenant di Azure. [Gateway VPN di Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) consente di stabilire connessioni tra reti virtuali e [tra una rete virtuale e una rete locale](vpn-gateway/vpn-gateway-plan-design.md). È possibile controllare completamente i blocchi di indirizzi IP, le impostazioni DNS, i criteri di sicurezza e le tabelle di route all'interno di una rete virtuale.  
Vedere [Panoramica di Rete virtuale](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>app Web
Nome alternativo per [app del servizio app](#app-service-app).

## <a name="see-also"></a>Vedere anche 

* [Inizia a usare Azure](https://azure.microsoft.com/get-started/)
* [Centro risorse cloud](https://azure.microsoft.com/resources/)  
* [Azure per le applicazioni aziendali](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure nel data center](https://azure.microsoft.com/overview/business-apps-on-azure/)

