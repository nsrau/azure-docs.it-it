<properties
	pageTitle="Utilizzo dei connettori del proxy di applicazione di Azure AD | Microsoft Azure"
	description="Illustra come creare e gestire i gruppi di connettori nel proxy di applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="kgremban"/>


# Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori - Anteprima pubblica

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portale di Azure classico](active-directory-application-proxy-connectors.md)


I gruppi di connettori sono utili in varie situazioni, ad esempio:

- Siti con più data center interconnessi. In questo caso è preferibile mantenere la maggior quantità di traffico possibile all'interno del data center, perché i collegamenti tra data center sono lenti e dispendiosi. È possibile distribuire connettori in ogni data center per rendere disponibili solo le applicazioni che risiedono all'interno del data center. Questo approccio riduce al minimo i collegamenti tra data center e offre un'esperienza completamente trasparente agli utenti.
- Gestione delle applicazioni installate in reti isolate che non fanno parte della rete aziendale principale. È possibile usare gruppi di connettori per installare connettori dedicati in reti isolate, in modo da isolare anche le applicazioni per la rete.
- Per le applicazioni installate in IaaS per l'accesso al cloud, i gruppi di connettori offrono un servizio comune per proteggere l'accesso a tutte le app. I gruppi di connettori non creano dipendenza aggiuntiva dalla rete aziendale o non frammentano l'esperienza delle app. I connettori possono essere installati in ogni data center nel cloud e gestire solo le applicazioni che si trovano nella rete. È possibile installare più connettori per ottenere una disponibilità elevata.
- Supporto per gli ambienti a più foreste in cui è possibile distribuire connettori specifici per ogni foresta e impostarli per gestire applicazioni specifiche.
- I gruppi di connettori possono essere usati nei siti di ripristino di emergenza per il rilevamento del failover o come backup per il sito principale.
- I gruppi di connettori possono essere anche usati per offrire i servizi a più società da un singolo tenant.

## Prerequisito: creare i connettori
Per raggruppare i connettori è necessario assicurarsi di avere [installato più connettori](active-directory-application-proxy-enable.md). Quando si installa un nuovo connettore, questo si aggiunge automaticamente al gruppo di connettori **predefinito**.

## Passaggio 1: Creare gruppi di connettori
È possibile creare tutti i gruppi di connettori desiderati. La creazione di un gruppo di connettori viene eseguita nel [portale di Azure](https://portal.azure.com).

1. Selezionare **Azure Active Directory** per passare al dashboard di gestione per la directory. Da qui selezionare **Applicazioni aziendali** > **Proxy dell'applicazione**.

2. Selezionare il pulsante **Connector Groups** (Gruppi di connettori). Viene visualizzato il pannello New Connector Group (Nuovo gruppo di connettori).

3. Assegnare un nome al nuovo gruppo di connettori, quindi usare il menu a discesa per selezionare i connettori appartenenti a questo gruppo.

4. Selezionare **Salva** al termine dell'operazione.

## Passaggio 2: Assegnare applicazioni ai gruppi di connettori
L'ultimo passaggio prevede l'assegnazione di ogni applicazione al gruppo di connettori da cui verrà gestita.

1. Nel dashboard di gestione per la directory selezionare **Applicazioni aziendali** > **All applications** (Tutte le applicazioni) > l'applicazione che si vuole assegnare a un gruppo di connettori > **Proxy dell'applicazione**.
2. In **Gruppo di connettori** usare il menu a discesa per selezionare il gruppo che si vuole venga usato dall'applicazione.
3. Fare clic su **Salva** per salvare la modifica.


## Vedere anche

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (Blog del proxy di applicazione)

<!---HONumber=AcomDC_0914_2016-->