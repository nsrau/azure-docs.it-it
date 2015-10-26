<properties
	pageTitle="Utilizzo dei connettori del proxy di applicazione di Azure AD"
	description="Illustra come creare e gestire i gruppi di connettori nel proxy di applicazione di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="rkarlin"/>


# Pubblicare applicazioni in reti e posizioni separate tramite i gruppi di connettori

> [AZURE.NOTE]Il Proxy applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-edition.md).

I gruppi di connettori sono utili in varie situazioni, ad esempio:


- Siti con più data center interconnessi in cui si desidera mantenere la maggior parte del traffico possibile all'interno del data center, senza usare collegamenti tra data center generalmente onerosi e con latenza elevata. È possibile distribuire i connettori in ogni data center per gestire solo le applicazioni che risiedono all'interno del data center ed evitare di usare i preziosi collegamenti tra data center, fornendo comunque un'esperienza del tutto trasparente agli utenti.
- Gestione delle applicazioni installate in reti isolate che non fanno parte della rete aziendale principale. È possibile usare gruppi di connettori per installare connettori dedicati per le reti isolate, rendendo anche possibile l'isolamento delle applicazioni per la rete.
- Per le applicazioni installate su IaaS per l'accesso dal cloud, i gruppi di connettori offrono un servizio comune per proteggere l'accesso a tutte le applicazioni, senza creare ulteriore dipendenza dalla rete aziendale o frammentare l'esperienza. I connettori possono essere installati in ogni data center nel cloud e gestire solo le applicazioni che si trovano nella rete. È possibile installare più connettori per ottenere una disponibilità elevata.
- Supporto per gli ambienti a più foreste in cui connettori specifici possono essere distribuiti per ogni foresta e impostati per gestire applicazioni specifiche.
- I gruppi di connettori possono essere usati nei siti di ripristino di emergenza per il rilevamento del failover o come backup per il sito principale.
- I gruppi di connettori possono essere anche usati per offrire i servizi a più società da un singolo tenant.


## Uso dei gruppi di connettori
Per raggruppare i connettori, è necessario assicurarsi di aver [installato più connettori](active-directory-application-proxy-enable.md). Poi sarà necessario assegnare loro un nome e quindi raggrupparli. Infine, è necessario assegnali ad applicazioni specifiche.

## Passaggio 1: Creare i gruppi di connettori
È possibile creare il numero desiderato di gruppi di connettori. La creazione di un gruppo di connettori viene eseguita nel portale di Azure. Selezionare la directory e fare clic su **Configura**. In Proxy dell'applicazione fare clic su **Gestisci gruppi di connettori** e creare un nuovo gruppo di connettori assegnando un nome al gruppo: ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png) ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)
## Passaggio 2: Assegnare i connettori ai gruppi
Dopo aver creato i gruppi di connettori, spostare i connettori nel gruppo appropriato. In **Proxy dell'applicazione** fare clic su **Gestisci connettori** e in **Gruppo** selezionare il gruppo desiderato per ogni connettore. Tenere presente che potrebbero essere richiesti fino a 10 minuti per l'attivazione dei connettori nel nuovo gruppo. ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)
## Passaggio 3: Assegnare applicazioni ai gruppi di connettori
L'ultimo passaggio prevede l'assegnazione di ogni applicazione al gruppo di connettori da cui verrà gestita. Nel portale di Azure, nella directory, selezionare l'applicazione che si desidera assegnare al gruppo e fare clic su **Configura**. In **Gruppo di connettori** selezionare il gruppo che si desidera venga usato dall'applicazione. Questa modifica viene applicata immediatamente. ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png) Per altre informazioni sulla pubblicazione di applicazioni, vedere [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
## Vedere anche
Si può fare molto di più con il proxy dell'applicazione:

- [Abilitare il proxy dell’applicazione](active-directory-application-proxy-enable.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

## Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive

* [Informazioni sulla delega vincolata Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=Oct15_HO3-->