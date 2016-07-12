<properties
	pageTitle="Servizi di dominio Active AD: Creare o selezionare una rete virtuale | Microsoft Azure"
	description="Introduzione a Servizi di dominio Azure Active Directory (anteprima)"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Servizi di dominio Active Directory *(anteprima)*: Creare o selezionare una rete virtuale

## Linee guida per la selezione di una rete virtuale di Azure
Quando si seleziona una rete virtuale da usare con Servizi di dominio Azure AD, tenere presente le linee guida seguenti:

- Assicurarsi di selezionare una rete virtuale in un'area supportata da Servizi di dominio Azure AD. Per informazioni sulle aree di Azure in cui sono disponibili i Servizi di dominio Azure Active Directory, vedere la pagina [Servizi in base all'area](https://azure.microsoft.com/regions/#services/).

- Se si prevede di usare una rete virtuale esistente, assicurarsi che sia una rete virtuale regionale. Con Servizi di dominio Azure AD non è possibile usare reti virtuali che usano il meccanismo dei gruppi di affinità legacy. Sarà necessario eseguire la [migrazione delle reti virtuali legacy a reti virtuali regionali](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

- Se si prevede di usare una rete virtuale esistente, assicurarsi che non siano presenti server DNS personalizzati configurati per la rete virtuale. Servizi di dominio Azure Active Directory non supporta server DNS personalizzati o di tipo "BYO" (Bring Your Own).

- Se si prevede di usare una rete virtuale esistente, assicurarsi che non sia presente un dominio esistente con lo stesso nome di dominio disponibile nella rete virtuale. Ad esempio, si supponga che un dominio denominato 'contoso.com' sia già disponibile nella rete virtuale selezionata. Successivamente provare ad abilitare un dominio gestito di Servizi di dominio Azure AD con lo stesso nome di dominio, ovvero 'contoso.com', alla rete virtuale. Si verificherà un errore quando si prova ad abilitare Servizi di dominio Azure AD. L'errore è dovuto a conflitti di nomi per il nome di dominio nella rete virtuale. In questa situazione è necessario usare un nome diverso per configurare il dominio gestito di Servizi di dominio Azure AD. In alternativa, è possibile eseguire il deprovisioning del dominio esistente e quindi abilitare Servizi di dominio Azure AD.

- Selezionare la rete virtuale che ospita o ospiterà le macchine virtuali che richiedono l'accesso a Servizi di dominio Azure AD. Non sarà possibile spostare Servizi di dominio in una rete virtuale diversa dopo l'abilitazione del servizio.

- Servizi di dominio Azure AD non è supportato con le reti virtuali create usando Gestione risorse di Azure. È possibile [connettere una rete virtuale classica a una rete virtuale basata su Gestione risorse di Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md), per potere usare Servizi di dominio Azure AD in una rete virtuale creata con Gestione risorse di Azure.


## Attività 2: Creare una rete virtuale di Azure
L'attività di configurazione successiva consiste nel creare una rete virtuale di Azure in cui si vuole abilitare Servizi di dominio Azure AD. Se si dispone già di una rete virtuale esistente, che si preferisce usare, è possibile ignorare questo passaggio.

> [AZURE.NOTE] Assicurarsi che la rete virtuale di Azure che si crea o si sceglie di usare con Servizi di dominio Azure AD appartenga a un'area di Azure supportata da Servizi di dominio Azure AD. Per informazioni sulle aree di Azure in cui sono disponibili i Servizi di dominio Azure Active Directory, vedere la pagina [Servizi in base all'area](https://azure.microsoft.com/regions/#services/).

Sarà necessario prendere nota del nome della rete virtuale, per selezionare la rete virtuale corretta al momento dell'abilitazione di Servizi di dominio Azure AD in un passaggio di configurazione successivo.

Eseguire i passaggi di configurazione seguenti per creare una rete virtuale di Azure in cui si desidera abilitare Servizi di dominio Azure AD.

1. Accedere al **portale di Azure classico** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selezionare il nodo **Reti** nel riquadro sinistro.

3. Fare clic su **NUOVO** nel riquadro attività nella parte inferiore della pagina.

    ![Nodo Reti virtuali](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Nel nodo **Servizi di rete** selezionare **Rete virtuale**.

5. Fare clic su **Creazione rapida** per creare una rete virtuale.

    ![Rete virtuale - creazione rapida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Specificare un **Nome** per la rete virtuale. È inoltre possibile scegliere di configurare i campi **Spazio di indirizzi** o **Numero massimo VM** per la rete. Per il momento è possibile confermare l'impostazione 'Nessuno' per il server DNS. Questa impostazione verrà aggiornata dopo l'abilitazione di Servizi di dominio Azure AD.

7. Assicurarsi di selezionare un'area di Azure supportata nell'elenco a discesa **Percorso**. Per informazioni sulle aree di Azure in cui sono disponibili i Servizi di dominio Azure Active Directory, vedere la pagina [Servizi in base all'area](https://azure.microsoft.com/regions/#services/). Questo passaggio è importante. Se si seleziona una rete virtuale in un'area di Azure non supportata da Servizi di dominio Azure AD, non sarà possibile abilitare il servizio in tale area.

8. Fare clic sul pulsante **Crea rete virtuale** per creare la rete virtuale.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## Attività 3: Abilitare Servizi di dominio Azure AD
L'attività di configurazione successiva consiste nell'[Abilitare Servizi di dominio Azure AD](active-directory-ds-getting-started-enableaadds.md).

<!---HONumber=AcomDC_0706_2016-->