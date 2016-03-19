<properties
    pageTitle="Come pianificare la rete virtuale per una raccolta Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come pianificare la rete virtuale per una raccolta Azure RemoteApp."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="elizapo" />

# Come pianificare la rete virtuale per Azure RemoteApp

Questo documento descrive come configurare la rete virtuale di Azure e la subnet per Azure RemoteApp. Se non si ha familiarità con le reti virtuali di Azure, si tratta di una funzionalità che consente di virtualizzare l'infrastruttura di rete nel cloud e di creare soluzioni ibride con Azure e le risorse locali. Per altre informazioni, leggere [qui](../virtual-network/virtual-networks-overview.md).

Per definire i criteri di protezione per il traffico, sia in ingresso che in uscita, nella rete virtuale in cui si distribuirà Azure RemoteApp, è consigliabile creare una subnet per Azure RemoteApp separata dal resto delle distribuzioni nella rete virtuale di Azure. Per altre informazioni sulla definizione dei criteri di sicurezza della subnet della rete virtuale di Azure, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

## Tipi di raccolte Azure RemoteApp con reti virtuali di Azure

L'immagine seguente mostra le due diverse opzioni della raccolta per l'uso di una rete virtuale.

### Raccolta nel cloud di Azure RemoteApp con la rete virtuale

 ![Azure RemoteApp - Raccolta nel cloud con una rete virtuale](./media/remoteapp-planvpn/ra-cloudvpn.png)

L'immagine rappresenta una raccolta Azure RemoteApp dove tutte le risorse a cui gli host sessione di RemoteApp devono accedere sono distribuire in Azure. Possono trovarsi nella stessa rete virtuale, ad esempio la rete virtuale di RemoteApp, o in una rete virtuale di Azure diversa.

### Raccolta ibrida di Azure RemoteApp con la rete virtuale

![Azure RemoteApp - Raccolta ibrida con una rete virtuale](./media/remoteapp-planvpn/ra-hybridvpn.png)

L'immagine rappresenta una raccolta Azure RemoteApp dove alcune risorse a cui gli host sessione di RemoteApp devono accedere sono distribuire in locale. La rete virtuale di RemoteApp è collegata alla rete locale usando le tecnologie ibride di Azure, ad esempio VPN da sito a sito o Express Route.


## Funzionamento del sistema

Dietro le quinte Azure RemoteApp distribuisce le macchine virtuali di Azure (con l'immagine caricata dall'utente) alla subnet della rete virtuale scelta durante il provisioning. Se si è scelta una raccolta ibrida, la funzionalità prova a risolvere il nome di dominio completo del controller di dominio immesso nel flusso di lavoro di provisioning con il server DNS fornito nella rete virtuale. Se ci si connette a una rete virtuale esistente, assicurarsi di esporre le porte necessarie nei gruppi di sicurezza di rete della subnet di Azure RemoteApp.

È consigliabile usare una [subnet sufficientemente estesa per Azure RemoteApp](remoteapp-vnetsizing.md). La più estesa supportata da una rete virtuale di Azure è /8 (usando le definizioni della subnet CIDR). La subnet deve essere sufficientemente estesa per contenere tutte le macchine virtuali di Azure RemoteApp nel momento in cui si verifica un aumento delle risorse quando più utenti accedono alle app.

Di seguito sono elencati gli elementi che è necessario abilitare nella subnet della rete virtuale:

2.	Consentire il traffico in uscita dalla subnet sull'intervallo di porte da 10101 a 10175 per comunicare con uno dei servizi interni di Azure RemoteApp.
3.	Consentire il traffico in uscita dalla subnet per connettersi al servizio di archiviazione di Azure sulla porta 443.
4.	Se Active Directory è ospitato in Azure, assicurarsi che tutte le macchine virtuali nella subnet della rete virtuale per Azure RemoteApp possano connettersi al controller di dominio. Il DNS della rete virtuale dovrà essere in grado di risolvere il nome di dominio completo di questo controller di dominio.


## Rete virtuale con tunneling forzato

Il [tunneling forzato](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) ora è supportato per tutte le nuove raccolte Azure RemoteApp. Attualmente non è supportata la migrazione di una raccolta esistente per supportare il tunneling forzato. Sarà necessario eliminare tutte le raccolte esistenti usando la rete virtuale collegata ad Azure RemoteApp e crearne di nuove per abilitare il tunneling forzato nelle raccolte.

<!---HONumber=AcomDC_0211_2016-->