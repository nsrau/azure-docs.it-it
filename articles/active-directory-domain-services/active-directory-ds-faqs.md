<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: Domande frequenti | Microsoft Azure"
	description="Domande frequenti su Servizi di dominio Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Anteprima di Servizi di dominio Azure Active Directory: Domande frequenti

Questa pagina include le risposte a domande frequenti relative all'anteprima di Servizi di dominio Azure AD. Controllarla costantemente per eventuali aggiornamenti.

### 1\. Configurazione

#### È possibile creare più domini per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Servizi di dominio Azure AD per una singola directory di Azure AD.

#### È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Servizi di dominio Active Directory di Azure è disponibile in una sola rete virtuale alla volta. È comunque possibile configurare la connettività tra più reti virtuali per esporre Servizi di dominio Azure AD ad altre reti virtuali. Questo articolo descrive come [connettere più reti virtuali in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnetconnection.md).

#### È possibile abilitare Servizi di dominio Azure AD con PowerShell?
PowerShell e la distribuzione automatizzata di Servizi di dominio Azure AD non sono attualmente disponibili.

#### Servizi di dominio Azure AD è disponibile nel nuovo portale di Azure?
No. È possibile configurare Servizi di dominio Azure AD solo nel portale di gestione di Azure precedente (ovvero https://manage.windowsazure.com). Microsoft prevede di estendere il supporto per il nuovo portale di gestione di Microsoft Azure (ovvero https://portal.azure.com) in futuro.


### 2\. Amministrazione e gestione

#### Dopo aver abilitato Servizi di dominio Azure AD, quale account utente è necessario usare per aggiungere i computer a questo dominio?
Gli account utente aggiunti al gruppo amministrativo (ovvero 'AAD DC Administrators') potranno aggiungere computer al dominio. Gli utenti di questo gruppo, inoltre, sono autorizzati per l'accesso con Desktop remoto ai computer aggiunti al dominio.

#### È possibile esercitare i privilegi di amministratore di dominio per il dominio fornito da Servizi di dominio Azure AD?
No. Trattandosi di un servizio gestito, non si disporrà dei privilegi amministrativi per il dominio. Questo significa che sia i privilegi di amministratore di dominio che di amministratore dell'organizzazione non saranno disponibili nel dominio. I gruppi di amministratori del dominio o di amministratori dell'organizzazione esistenti nella directory di Azure AD, inoltre, non disporranno di privilegi di amministratore del dominio/dell'organizzazione per questo dominio.

#### È possibile modificare le appartenenze ai gruppi tramite LDAP o altri strumenti di amministrazione di Active Directory nei domini forniti da Servizi di dominio Azure AD?
No. Le appartenenze ai gruppi non possono essere modificate nei domini gestiti da Servizi di dominio Azure AD. Lo stesso vale per gli attributi utente. È tuttavia possibile modificare le appartenenze ai gruppi o gli attributi utente in Azure AD o nel dominio locale. Tali modifiche verranno sincronizzate automaticamente in Servizi di dominio Azure AD.


### 3\. Fatturazione e disponibilità

#### Si tratta di un servizio a pagamento?
Il servizio è disponibile a un prezzo ridotto speciale per la durata del periodo di anteprima pubblica. La fatturazione inizierà al prezzo intero quando il servizio sarà disponibile a livello generale. Per altre informazioni vedere la pagina dei prezzi.

#### È disponibile una versione di valutazione gratuita per il servizio?
Questo servizio è incluso nella versione di valutazione gratuita di Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

#### È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)?
No. Servizi di dominio Azure AD è un servizio di Azure con pagamento in base al consumo e non fa parte di EMS. Servizi di dominio Azure AD è disponibile per tutti gli SKU di Azure AD (ovvero Free, Basic e Premium) e il servizio viene fatturato su base oraria in base all'utilizzo.

#### In quale aree di Azure è disponibile il servizio?
Per un elenco di aree di Azure per cui è disponibile Servizi di dominio Azure AD, fare riferimento alla [pagina delle aree](active-directory-ds-regions.md).

#### Quando sarà disponibile a livello generale Servizi di dominio Azure AD?
Non è attualmente possibile condividere informazioni sulla tempistica prevista per la disponibilità del servizio a livello generale.

<!---HONumber=Oct15_HO3-->