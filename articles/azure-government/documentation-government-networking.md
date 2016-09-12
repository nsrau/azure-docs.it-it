<properties
	pageTitle="Documentazione di Azure per enti pubblici | Microsoft Azure"
	description="Fornisce un confronto di funzionalità e informazioni aggiuntive per la connettività privata all'eGovernment"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>

#  Rete di Azure per enti pubblici

##  ExpressRoute (connettività privata)

Esistono due servizi di base che forniscono la connettività di rete privata in Azure per enti pubblici: VPN (da sito a sito per un'organizzazione tipica) ed ExpressRoute.

Azure ExpressRoute si usa per creare connessioni private tra i data center di Azure per enti pubblici e l'infrastruttura locale o in un ambiente di housing. Le connessioni ExpressRoute non usano la rete Internet pubblica. Offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso delle connessioni ExpressRoute per trasferire dati tra sistemi locali e Azure offre vantaggi significativi in termini di costi.

Con ExpressRoute si possono stabilire connessioni con Azure in una località ExpressRoute, ad esempio la struttura di un provider di Exchange, o connettersi direttamente ad Azure dalla rete WAN esistente, ad esempio una VPN MPLS (Multiprotocol Label Switching), fornita da un provider di servizi di rete.

![testo alternativo](./media/azure-government-capability-private-connectivity-options.PNG) ![testo alternativo](./media/government-capability-expressroute.PNG)

Perché i servizi di rete possano supportare le applicazioni e le soluzioni dei clienti di Azure per enti pubblici, è consigliabile implementare ExpressRoute (connettività privata) per connettersi ad Azure per enti pubblici. Se si usano connessioni VPN, è opportuno tenere in considerazione i punti seguenti:

• I clienti devono contattare il funzionario o l'ente competente per l'autorizzazione per determinare se è richiesta la connettività privata o un altro meccanismo di connessione sicura e identificare eventuali restrizioni da considerare.

• I clienti devono decidere se imporre che la connessione VPN da sito a sito venga instradata attraverso una zona con connettività privata.

• I clienti possono ottenere un circuito MPLS o una VPN con un provider di accesso alla connettività privata con licenza.

Tutti i clienti che utilizzano un'architettura di connettività privata devono accertarsi che venga stabilita e mantenuta un'implementazione appropriata per la connessione dei clienti al punto di demarcazione del router perimetrale gateway di rete/Internet per Azure per enti pubblici. In modo analogo, l'organizzazione deve stabilire la connettività di rete tra l'ambiente locale e il punto di demarcazione del router perimetrale gateway di rete/Internet per Azure per enti pubblici.

ExpressRoute è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, compresi i partner e le località di peering, vedere la <a href="https://azure.microsoft.com/documentation/services/expressroute/"> documentazione pubblica su ExpressRoute </a>.

Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>

<!---HONumber=AcomDC_0831_2016-->