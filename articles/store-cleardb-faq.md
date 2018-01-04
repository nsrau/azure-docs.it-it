---
title: Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure | Microsoft Docs
description: Risposte a domande comuni sull'uso di database MySQL ClearDB con il Servizio app di Azure.
documentationcenter: php
services: 
author: sunbuild
manager: yochayk
editor: 
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: 0d1e3c826e61b8dee264e1c01fd2d54ba7ebb636
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Domande frequenti per i database MySQL ClearDB con il Servizio app di Azure
Queste domande frequenti offrono risposte a domande comuni sull'uso e l'acquisto di database MySQL ClearDB per App Web di Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quali sono le opzioni disponibili per MySQL in Azure?
Sono disponibili diverse opzioni:

* [Database MySQL condiviso ClearDB](/marketplace/partners/cleardb/databases/)
* [Cluster Premium MySQL ClearDB](/marketplace/partners/cleardb-clusters/cluster/)
* [Cluster MySQL in esecuzione su una VM di Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Istanza singola di MySQL in esecuzione su una VM di Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB è un servizio di hosting MySQL e gestisce l'infrastruttura di MySQL per l'utente. Quando si esegue il database o il cluster MySQL in una macchina virtuale di Azure, è necessario configurare il server MySQL e mantenerlo aggiornato con le patch.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>È necessaria una carta di credito per l'app Web e il modello MySQL in Azure Marketplace?
Questo dipende dal tipo di sottoscrizione in uso. Di seguito sono riportati alcuni tipi di sottoscrizione di uso comune:

* [Pagamento in base al consumo](/offers/ms-azr-0003p/): richiede una carta di credito sulla quale addebitare i costi dell'acquisto di un database MySQL a pagamento.
* [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/): include crediti da usare con i servizi di Microsoft Azure, ma non consente di acquistare risorse di terze parti. Per acquistare servizi di terze parti o un database MySQL a pagamento, è necessario usare una sottoscrizione abilitata con carta di credito. Per le app Web, è possibile creare un database MySQL ClearDB GRATUITO.
* [Sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **Pagamento in base al consumo per sviluppo/test MSDN**: analogamente alla versione di valutazione gratuita, una sottoscrizione MSDN richiede una carta di credito per l'acquisto di una soluzione a pagamento MySQL da ClearDB.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA fatturazione contro i EA ogni trimestre per tutti gli acquisti di Azure Marketplace (di terze parti) in una fattura separata, consolidato. I costi degli eventuali acquisti effettuati nel marketplace vengono addebitati al di fuori dell'impegno monetario. Si noti che al momento Azure Store non è disponibile per i clienti iscritti in Azerbaigian, Croazia, Norvegia e Porto Rico. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): è possibile creare solo database ClearDB gratuiti per le app Web. Non sono previsti limiti al numero di database MySQL ClearDB gratuiti che è possibile creare. Si noti che i database gratuiti non devono essere usati per le app Web di produzione, in quanto il servizio è previsto solo a scopo di valutazione.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Perché è stato ricevuto un addebito di $ 3,50 per un'app Web + MySQL da Azure Marketplace?
L'opzione predefinita del database è Titan, che ha un costo di $ 3,50. Durante la creazione del database non viene visualizzato il costo e si potrebbe acquistare erroneamente un database che non si voleva acquistare. L'esperienza è in corso di miglioramento, ma finché non è disponibile una soluzione è necessario controllare tutti i piani tariffari selezionati per app Web e database prima di scegliere **Crea** e avviare la distribuzione delle risorse.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Si esegue MySQL sulla propria macchina virtuale di Azure. È possibile connettere la propria app Web di Azure al database?
Sì. È possibile connettere la propria app Web al database, purché la VM di Azure abbia accesso remoto all'app Web. Per altre informazioni, vedere l'articolo relativo all' [installazione di MySQL in una macchina virtuale](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>In quali paesi sono supportati i cluster MySQL Premium ClearDB?
[cluster MySQL Premium ClearDB](/marketplace/partners/cleardb-clusters/cluster/) sono disponibili in tutte le aree di Azure in tutto il mondo a eccezione dell'India, dell'Australia, del Brasile meridionale e della Cina.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>È possibile creare un nuovo cluster prima di creare un database con la soluzione cluster Premium ClearDB?
No, la creazione di cluster ClearDB vuoti non è supportata. Il Portale di Azure consente di creare database in un cluster, eventualmente creando un nuovo cluster nello stesso momento.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Si riceve un avviso se si cerca di eliminare un database MySQL ClearDB usato da una delle applicazioni?
No, Azure non invierà alcuna notifica se si elimina un acquisto di Marketplace da cui dipende l'applicazione.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>In quali aree è possibile creare database ClearDB?
Azure Marketplace non è disponibile per i clienti iscritti in Azerbaigian, Croazia, Norvegia e Porto Rico. ClearDB non è disponibile in queste aree.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Quale piano tariffario scegliere per un'app Web e un database di produzione?
Usare il piano tariffario Basic o superiore per le app Web. Per ClearDB è consigliabile un piano Saturn o Jupiter. Esaminare le funzioni e le limitazioni di ogni piano tariffario sia per le [App Web](https://azure.microsoft.com/pricing/details/app-service/) che per i [database MySQL ClearDB](/marketplace/partners/cleardb/databases/) per scegliere il più adatto alle proprie esigenze.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Come si aggiorna il database ClearDB da un piano a un altro?
Nel [Portale di Azure](https://portal.azure.com) è possibile aumentare le prestazioni di un database ClearDB in hosting condiviso. Per altre informazioni, vedere [questo articolo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/). Attualmente il Portale di Azure non supporta l'aggiornamento dei cluster ClearDB Premium.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Non è possibile visualizzare il database ClearDB nel Portale di Azure?
Se è stato creato un database ClearDB classica, non sarà in grado di visualizzare il database nel [portale Azure](https://portal.azure.com). Non sussiste alcuna soluzione alternativa per questo scenario.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Chi è possibile contattare per ottenere aiuto quando il database presenta un problema?
Per tutti i problemi relativi al database, contattare il [supporto ClearDB](https://www.cleardb.com/developers/help/support) . Tenersi pronti a comunicare le informazioni relative alla sottoscrizione di Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>È possibile creare altri utenti dalla soluzione cluster database MySQL ClearDB?
di serie Non è possibile creare altri utenti, ma è possibile creare altri database nel cluster database ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>È possibile aggiornare database Basic/Pro in loco in modo allo stesso modo dei piani Planetary nel portale di ClearDB?
Sì, i database della serie Basic possono essere aggiornati sul posto (da Basic 60 a Basic 500). Quelli della serie Pro possono essere aggiornati sul posto (da Pro 125 a Pro 1000) ad eccezione di Pro 60. L'aggiornamento del database Pro 60 attualmente non è supportato. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando si esegue la migrazione delle risorse da una sottoscrizione a un'altra, viene eseguita anche la migrazione del database MySQL ClearDB?
Quando si esegue la migrazione delle risorse tra le sottoscrizioni, esistono alcune [limitazioni](app-service/app-service-move-resources.md) . Il database MySQL ClearDB è un servizio di terze parti, pertanto non è possibile eseguirne la migrazione durante la migrazione delle sottoscrizioni di Azure. Se non si gestisce la migrazione del database MySQL prima della migrazione delle risorse di Azure, i database MySQL ClearDB possono essere disabilitati. Eseguire prima la migrazione manuale dei database e quindi eseguire la migrazione delle sottoscrizioni di Azure per l'app Web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Il limite di spesa per la sottoscrizione è stato raggiunto. Il limite è stato rimosso e il servizio app è online, ma il database non è comunque accessibile. Come si abilita nuovamente il database ClearDB?
Contattare il [supporto di ClearDB](https://www.cleardb.com/developers/help/support) per abilitare nuovamente il database. Comunicare le informazioni relative alla sottoscrizione di Azure e il nome del database.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>È possibile trasferire un database ClearDB da una sottoscrizione con carta di credito a una sottoscrizione con contratto Enterprise?
I database ClearDB esistenti usano la carta di credito associata alle sottoscrizioni esistenti. Per usare una sottoscrizione con contratto Enterprise è necessario eseguire la migrazione dei dati in un nuovo database:

* Acquistare un nuovo database ClearDB con la sottoscrizione con contratto Enterprise.
* Eseguire la migrazione dei dati nel nuovo database.
* Aggiornare l'applicazione per usare il nuovo database.
* Eliminare il vecchio database ClearDB.

Quando si crea una nuova app Web con MySQL (ClearDB) o un database MySQL (ClearDB), la sottoscrizione che si sceglie determina la modalità di pagamento per il servizio. Con la sottoscrizione del contratto Enterprise Agreement, l'approvvigionamento dei servizi di terze parti, come ClearDB, nel portale di Azure non verrà bloccato. Le sottoscrizioni con contratto Enterprise vengono addebitate al di fuori dell'impegno monetario e vengono fatturate su base trimestrale e in modo posticipato. Per pagare i servizi Marketplace di terze parti, i clienti con contratto Enterprise Agreement devono impostare un metodo di pagamento, ad esempio una carta di credito.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Dove è possibile visualizzare gli addebiti per le risorse di ClearDB in una sottoscrizione con contratto Enterprise?
Per i clienti con contratto Enterprise diretti, gli addebiti di Azure Marketplace sono visibili in Enterprise Portal. Si noti che tutti gli acquisti e l'uso di Marketplace vengono addebitati al di fuori dell'impegno monetario e vengono fatturati su base trimestrale e in modo posticipato. I clienti con contratto Enterprise Agreement devono pagare direttamente i provider di servizi di terze parti, attivando un metodo di pagamento, ad esempio una carta di credito, tramite l'account del contratto Enterprise Agreement.

I clienti con contratto Enterprise indiretti possono trovare le proprie sottoscrizioni di Azure Marketplace nella pagina **Gestisci sottoscrizioni** di Enterprise Portal, tuttavia i prezzi sono nascosti. I clienti devono contattare il proprio LSP per informazioni sugli addebiti di Marketplace.

L'accesso ad Azure Marketplace per i servizi di terze parti può essere gestito dagli amministratori dell'iscrizione ad Azure con contratto Enterprise Agreement. Questi ultimi possono disabilitare o riabilitare l'accesso ad acquisti di terze parti dallo Store nell'opzione per la gestione degli account e delle sottoscrizioni nella sezione Account di Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Chi contattare per domande sulla fatturazione per i servizi ClearDB nella sottoscrizione con contratto Enterprise?
Contattare il [supporto per contratti Enterprise](http://aka.ms/AzureEntSupport) per quanto riguarda la fatturazione relativa alla registrazione con contratto Enterprise. Il team di supporto del portale per contratti Enterprise risponderà alla domanda oppure offrirà aiuto per la risoluzione del problema.

## <a name="more-information"></a>Altre informazioni
[Domande frequenti su Azure Marketplace](/marketplace/faq/)

