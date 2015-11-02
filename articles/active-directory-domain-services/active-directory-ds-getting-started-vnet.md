<properties
	pageTitle="Anteprima di Servizi di dominio Azure Active Directory: Introduzione | Microsoft Azure"
	description="Introduzione a Servizi di dominio Azure Active Directory"
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

# Servizi di dominio Azure AD *(anteprima)* - Introduzione

## Linee guida per la selezione di una rete virtuale di Azure
Quando si seleziona una rete virtuale da usare con Servizi di dominio Azure AD, tenere presente le linee guida seguenti:

- Assicurarsi di selezionare una rete virtuale in un'area supportata da Servizi di dominio Azure AD. L'elenco corrente delle aree di Azure supportate è disponibile nella [pagina delle aree](active-directory-ds-regions.md).
- Se si prevede di usare una rete virtuale esistente, assicurarsi che sia una rete virtuale regionale. Con Servizi di dominio Azure AD non è possibile usare reti virtuali che usano il meccanismo dei gruppi di affinità legacy. Sarà necessario eseguire la [migrazione delle reti virtuali legacy a reti virtuali regionali](../virtual-networks-migrate-to-regional-vnet.md).
- Selezionare la rete virtuale che ospita o ospiterà le macchine virtuali che richiedono l'accesso a Servizi di dominio Azure AD. Non sarà possibile spostare Servizi di dominio in un'altra rete virtuale in seguito.


## Passaggio 2: Creare una rete virtuale di Azure
Il passaggio di configurazione successivo consiste nel creare una rete virtuale di Azure in cui si desidera abilitare Servizi di dominio Azure AD. Se si dispone già di una rete virtuale esistente, che si preferisce usare, è possibile ignorare questo passaggio.

> [AZURE.NOTE]Assicurarsi che la rete virtuale di Azure che si crea o si sceglie di usare con Servizi di dominio Azure AD appartenga a un'area di Azure supportata da Servizi di dominio Azure AD. Per un elenco di aree di Azure per cui è disponibile Servizi di dominio Azure AD, fare riferimento alla pagina delle [aree](active-directory-ds-regions.md).

Sarà necessario prendere nota del nome della rete virtuale, per selezionare la rete virtuale corretta al momento dell'abilitazione di Servizi di dominio Azure AD in un passaggio di configurazione successivo.

Eseguire i passaggi di configurazione seguenti per creare una rete virtuale di Azure in cui si desidera abilitare Servizi di dominio Azure AD.

1. Accedere al **portale di gestione di Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Selezionare il nodo **Reti** nel riquadro sinistro.
3. Fare clic su **NUOVO** nel riquadro attività nella parte inferiore della pagina.

    ![Nodo Reti virtuali](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Nel nodo **Servizi di rete** selezionare **Rete virtuale**.
5. Fare clic su **Creazione rapida** per creare una rete virtuale.

    ![Rete virtuale - creazione rapida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Specificare un **Nome** per la rete virtuale. È inoltre possibile scegliere di configurare i campi **Spazio di indirizzi** o **Numero massimo VM** per la rete. Per il momento è possibile confermare l'impostazione 'Nessuno' per il server DNS. Questa impostazione verrà aggiornata dopo l'abilitazione di Servizi di dominio Azure AD.
7. Assicurarsi di selezionare un'area di Azure supportata nell'elenco a discesa **Percorso**. Per un elenco di aree di Azure per cui è disponibile Servizi di dominio Azure AD, fare riferimento alla pagina delle [aree](active-directory-ds-regions.md). Questo passaggio è importante. Se si seleziona una rete virtuale in un'area di Azure non supportata da Servizi di dominio Azure AD, non sarà possibile abilitare il servizio in tale area.
8. Fare clic sul pulsante **Crea rete virtuale** per creare la rete virtuale.

    ![Creare una rete virtuale per Servizi di dominio Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**Passaggio successivo - Abilitare Servizi di dominio Azure AD.**](active-directory-ds-getting-started-enableaadds.md)

<!---HONumber=Oct15_HO4-->