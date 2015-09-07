<properties
	pageTitle="Rete virtuale con le tabelle di configurazione | Microsoft Azure"
	description="Informazioni su come configurare una rete virtuale cross-premise di Azure usando le impostazioni di una tabella di configurazione contenente impostazioni predeterminate."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Creare una rete virtuale cross-premise utilizzando le tabelle di configurazione

I passaggi di questo argomento illustrano la creazione di una rete virtuale cross-premise utilizzando le impostazioni precedentemente specificate nel set di tabelle di configurazione seguente:

- Tabella V: Configurazione della rete virtuale cross-premise
- Tabella S: Subnet nella rete virtuale
- Tabella D: Server DNS locali
- Tabella L: Prefissi degli indirizzi per la rete locale

Queste tabelle in genere vengono compilate in un argomento che descrive la configurazione di un carico di lavoro IT in Azure e include una rete virtuale cross-premise. Per un esempio, vedere [Fase 1: Configurare Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

La procedura seguente fa riferimento alle informazioni in queste tabelle come guida nel processo di configurazione della rete virtuale. Se non sono già state specificate le impostazioni in queste tabelle in un altro argomento, ma si desidera comunque configurare una rete virtuale cross-premise, vedere [Configurazione di una connessione da sito a sito cross-premise a una rete virtuale di Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE]Questa procedura descrive la creazione di una rete virtuale che utilizza una connessione VPN da sito a sito. Per informazioni sull'utilizzo di Azure ExpressRoute per la connessione da sito a sito, vedere [Panoramica tecnica di ExpressRoute](../expressroute/expressroute-introduction.md).

## Creare una nuova rete virtuale di Azure cross-premise con le impostazioni delle tabelle di configurazione

1. Accedere al [portale di Azure](https://manage.windowsazure.com/).
2. Dalla barra delle applicazioni, fare clic su **Nuovo > Servizi di rete > Rete virtuale > Creazione personalizzata**.
3. Nella pagina **Dettagli della rete virtuale**:
	- In **Nome**, digitare il nome per l’elemento 1 nella tabella V.
	- In **Posizione**, selezionare l'area dall’elemento 2 nella tabella V.
4. Fare clic sulla freccia Avanti per continuare.
5. Nella pagina **Server DNS e connettività VPN**:
	- In **Server DNS**, per ogni voce nella tabella D, configurare il nome descrittivo e l'indirizzo IP dei server DNS locali.
	- In **Connettività da sito a sito**, selezionare **Configurare una VPN da sito a sito**.
	- Se è stata già configurata una rete locale e si desidera utilizzarla, selezionarne il nome in **Rete locale**. Se si desidera creare una nuova rete locale, selezionare **Specificare una nuova rete locale** in **Rete locale**.
	- Se non è stata già configurata una rete locale per la sottoscrizione, il campo **Rete locale** non sarà disponibile.
6. Fare clic sulla freccia Avanti per continuare.
7. Nella pagina **Connettività da sito a sito** (se nel passaggio 5 è stata selezionata l’opzione **Specifica una nuova rete locale**):
	- In **Nome**, digitare il nome dall’elemento 3 nella tabella V (il nome della rete locale).
	- In **Indirizzo IP del dispositivo VPN**, digitare l'indirizzo dall’elemento 4 nella tabella V.
	- In **Spazio degli indirizzi**, per ogni voce nella tabella L, immettere gli spazi degli indirizzi IP della rete dell'organizzazione in termini di prefisso (in **IP iniziale**) e lunghezza del prefisso (in **CIDR (conteggio indirizzi)**).
8. Fare clic sulla freccia Avanti per continuare.
9. Nella pagina **Spazi di indirizzi della rete virtuale**:
	- In **Spazio degli indirizzi**, immettere lo spazio degli indirizzi IP privato della rete virtuale dall’elemento 5 della tabella V in termini di prefisso (in **IP iniziale**) e lunghezza del prefisso (in **CIDR (conteggio indirizzi)**).
	- In **Subnet**, per ogni voce nella tabella S:
		- Digitare il nome della subnet nella colonna **Subnet**, sovrascrivendo il nome predefinito, se necessario.
		- Digitare lo spazio degli indirizzi IP privato della subnet, in termini di prefisso (in **IP iniziale**) e lunghezza del prefisso (in **CIDR (conteggio indirizzi)**).
	- Fare clic su **Aggiungi subnet gateway**.
10. Fare clic sul segno di spunta per completare la configurazione.

## Risorse aggiuntive

[Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).

[Attività di configurazione della rete virtuale](../documentation/services/virtual-machines/)

[Configurare una connessione cross-premise Site-to-Site a una rete virtuale di Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)

<!---HONumber=August15_HO9-->