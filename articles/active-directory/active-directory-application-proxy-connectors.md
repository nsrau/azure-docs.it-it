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


# Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-application-proxy-connectors-azure-portal.md)
- [Portale di Azure classico](active-directory-application-proxy-connectors.md)


I gruppi di connettori sono utili in varie situazioni, ad esempio:

- Siti con più data center interconnessi. In questo caso, è preferibile mantenere la maggior quantità di traffico possibile all'interno del data center, perché i collegamenti tra data center sono in genere lenti e dispendiosi. È possibile distribuire connettori in ogni data center per rendere disponibili solo le applicazioni che risiedono all'interno del data center. Questo approccio riduce al minimo i collegamenti tra data center e offre un'esperienza completamente trasparente agli utenti.
- Gestione delle applicazioni installate in reti isolate che non fanno parte della rete aziendale principale. È possibile usare gruppi di connettori per installare connettori dedicati in reti isolate, in modo da isolare anche le applicazioni per la rete.
- Per le applicazioni installate in IaaS per l'accesso al cloud, i gruppi di connettori offrono un servizio comune per proteggere l'accesso a tutte le app. I gruppi di connettori non creano dipendenza aggiuntiva dalla rete aziendale o non frammentano l'esperienza delle app. I connettori possono essere installati in ogni data center nel cloud e gestire solo le applicazioni che si trovano nella rete. È possibile installare più connettori per ottenere una disponibilità elevata.
- Supporto per gli ambienti a più foreste in cui è possibile distribuire connettori specifici per ogni foresta e impostarli per gestire applicazioni specifiche.
- I gruppi di connettori possono essere usati nei siti di ripristino di emergenza per il rilevamento del failover o come backup per il sito principale.
- I gruppi di connettori possono essere anche usati per offrire i servizi a più società da un singolo tenant.

## Prerequisito: creare i connettori
Per raggruppare i connettori è necessario assicurarsi di avere [installato più connettori](active-directory-application-proxy-enable.md). Poi sarà necessario assegnare un nome e quindi raggrupparli. Infine, è necessario assegnali ad applicazioni specifiche.

## Passaggio 1: Creare gruppi di connettori
È possibile creare tutti i gruppi di connettori desiderati. La creazione di un gruppo di connettori viene eseguita nel portale di Azure classico.

1. Selezionare la directory e fare clic su **Configura**. ![Schermata di configurazione del proxy dell'applicazione: fare clic su Gestisci gruppi di connettori](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. In Proxy dell'applicazione fare clic su **Gestisci gruppi di connettori** e creare un nuovo gruppo di connettori assegnando un nome al gruppo. ![Schermata dei gruppi del connettore del proxy dell'applicazione: assegnare un nome al gruppo](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## Passaggio 2: Assegnare i connettori ai gruppi
Una volta creati i gruppi di connettori, spostare i connettori nel gruppo appropriato.

1. In **Proxy dell'applicazione** fare clic su **Gestire connettori**.
2. In **Gruppo** selezionare il gruppo desiderato per ogni connettore. Potrebbero essere richiesti fino a 10 minuti per l'attivazione dei connettori nel nuovo gruppo. ![Schermata dei connettori proxy dell'applicazione: selezionare gruppo dal menu a discesa](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## Passaggio 3: Assegnare applicazioni ai gruppi di connettori
L'ultimo passaggio prevede l'assegnazione di ogni applicazione al gruppo di connettori da cui verrà gestita.

1. Nel portale di Azure classico selezionare nella directory l'applicazione che si vuole assegnare al gruppo e fare clic su **Configura**.
2. In **Gruppo di connettori** selezionare il gruppo che si desidera venga usato dall'applicazione. Questa modifica viene applicata immediatamente. ![Schermata dei gruppi del connettore proxy dell'applicazione: selezionare gruppo dal menu a discesa](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## Vedere anche

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (Blog del proxy di applicazione)

<!---HONumber=AcomDC_0914_2016-->