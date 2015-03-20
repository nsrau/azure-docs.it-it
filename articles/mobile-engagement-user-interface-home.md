<properties 
 pageTitle="Interfaccia utente di Azure Mobile Engagement - Home" 
 description="Panoramica dell'interfaccia utente di Azure Mobile Engagement - Sezione Home" 
 services="mobile-engagement" 
 documentationCenter="mobile" 
 authors="v-micada" 
 manager="mattgre" 
 editor=""/>

<tags
 ms.service="mobile-engagement"
 ms.devlang="Java"
 ms.topic="article"
 ms.tgt_pltfrm="mobile"
 ms.workload="required" 
 ms.date="02/17/2015"
 ms.author="v-micada"/>

# Azure Mobile Engagement - Interfaccia utente

<div class="dev-center-tutorial-selector sublanding">
<a href="../mobile-engagement-user-interface" title="Introduction">Introduzione</a>
<a href="../mobile-engagement-user-interface-navigation" title="Navigation">Navigazione</a>
<a href="../mobile-engagement-user-interface-home/" title="Home">Home</a>
<a href="../mobile-engagement-user-interface-my-account" title="My Account">Account personale</a>
<a href="../mobile-engagement-user-interface-analytics" title="Analytics">Analytics</a>
<a href="../mobile-engagement-user-interface-monitor" title="Monitor">Monitoraggio</a>
<a href="../mobile-engagement-user-interface-reach" title="Reach">Copertura</a>
<a href="../mobile-engagement-user-interface-segments" title="Segments">Segmenti</a>
<a href="../mobile-engagement-user-interface-dashboard" title="Dashboard">Dashboard</a>
<a href="../mobile-engagement-user-interface-settings" title="Settings">Impostazioni</a>
</div>

# Home
 
La sezione Home dell'interfaccia utente contiene l'elenco di tutte le applicazioni presenti in Applicazioni personali e offre la possibilità di concedere altre autorizzazioni alle applicazioni. Chiunque può accedere alla home page dell'interfaccia utente creando un account. Tuttavia, per consentire l'accesso alle applicazioni personalizzate presenti in Progetti personali è necessario concedere l'autorizzazione agli altri utenti.

**Vedere anche:** 

- [Guida alla risoluzione dei problemi - Assistenza][Link 2]
 
## Applicazioni personali:
 
![Home1][2]

Questa panoramica rapida delle applicazioni consente di selezionare l'applicazione da aprire per visualizzare in dettaglio le opzioni della barra multifunzione. È possibile selezionare il nome dell'applicazione per tornare al percorso della barra multifunzione visitato più di recente nell'applicazione oppure è possibile selezionare l'icona a ingranaggio per accedere direttamente alla pagina delle impostazioni dell'applicazione. È possibile cercare, filtrare oppure ordinare le informazioni visualizzate nelle tabelle delle applicazioni. È possibile trascinare e rilasciare le intestazioni della colonne per modificarne l'ordine. 
 
La panoramica delle applicazioni include:

- Utenti totali: il numero totale di utenti
- Tendenza nuovi utenti: evoluzione dei nuovi utenti nelle ultime due settimane
- Utenti attivi: numero di utenti attivi negli ultimi 30 giorni
- Tendenza utenti attivi: evoluzione degli utenti attivi nelle ultime due settimane
 
Inoltre, è possibile visualizzare un grafico di confronto delle applicazioni.

- Mostra grafico di confronto: da utilizzare per visualizzare i dati dell'applicazione in forma di grafico
- Caselle di controllo: consentono di aggiungere/rimuovere l'applicazione dal grafico di confronto
 
![Home2][3]

## Progetti personali:
 
![Home5][6]

È possibile utilizzare i progetti per raggruppare le applicazioni e concedere le autorizzazioni alle applicazioni stesse. Il pulsante Nuovi progetti consente di creare un nuovo progetto inserendo soltanto un "nome" e una "descrizione". Una volta creato un progetto, è possibile selezionarne il nome per modificare la descrizione e il nome del prodotto e per selezionare tutte le applicazioni che si desidera visualizzare nel progetto. Inoltre, è possibile eliminare il progetto, senza eliminare anche le applicazioni a cui fa riferimento. Tuttavia, l'utente perderà l'accesso a tutte le applicazioni che non possiede e alle quali non è possibile accedere da un altro progetto. Pertanto, prestare attenzione prima di effettuare questa operazione. 
È anche possibile invitare un utente al proprio progetto, utilizzando l'indirizzo di posta elettronica. È necessario che gli utenti abbiano già creato un account in Azure Mobile Engagement per poter ricevere le autorizzazioni. 

**I ruoli includono:** 

- Visualizzatore: un visualizzatore può soltanto visualizzare le applicazioni associate a un progetto. Un visualizzatore può accedere alle analisi, monitorare i dati e consultare i risultati Reach. Un visualizzatore non può modificare le informazioni, né gestire le applicazioni o gli utenti. Un visualizzatore non può creare o modificare lo stato di una campagna Reach.
- Sviluppatore: uno sviluppatore può eseguire le stesse attività di un visualizzatore nonché gestire le applicazioni. Uno sviluppatore può attivare o disattivare le applicazioni, modificare le informazioni delle applicazioni (pacchetto e firma, ad esempio) e creare campagne Reach. Uno sviluppatore non può gestire utenti. 
- Amministratore: un amministratore può eseguire tutte le attività di uno sviluppatore e gestire gli utenti. Un amministratore può invitare gli utenti a partecipare a un progetto, modificare i ruoli degli utenti e le informazioni del progetto. Nelle impostazioni è anche possibile impostare le autorizzazioni a livello di applicazione.
 
**Vedere anche:** 

- [Documentazione sull'interfaccia utente - Impostazioni][Link 20]

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: ../mobile-engagement-user-interface/
[Link 2]: ../mobile-engagement-troubleshooting-guide/
[Link 3]: ../mobile-engagement-how-tos/
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: ../mobile-engagement-user-interface-navigation/
[Link 13]: ../mobile-engagement-user-interface-home/
[Link 14]: ../mobile-engagement-user-interface-my-account/
[Link 15]: ../mobile-engagement-user-interface-analytics/
[Link 16]: ../mobile-engagement-user-interface-monitor/
[Link 17]: ../mobile-engagement-user-interface-reach/
[Link 18]: ../mobile-engagement-user-interface-segments/
[Link 19]: ../mobile-engagement-user-interface-dashboard/
[Link 20]: ../mobile-engagement-user-interface-settings/
[Link 21]: ../mobile-engagement-troubleshooting-guide-analytics/
[Link 22]: ../mobile-engagement-troubleshooting-guide-apis/
[Link 23]: ../mobile-engagement-troubleshooting-guide-push-reach/
[Link 24]: ../mobile-engagement-troubleshooting-guide-service/
[Link 25]: ../mobile-engagement-troubleshooting-guide-sdk/
[Link 26]: ../mobile-engagement-troubleshooting-guide-sr-info/
[Link 27]: ../mobile-engagement-how-tos-first-push/
[Link 28]: ../mobile-engagement-how-tos-test-campaign/
[Link 29]: ../mobile-engagement-how-tos-personalize-push/
[Link 30]: ../mobile-engagement-how-tos-differentiate-push/
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign/
[Link 32]: ../mobile-engagement-how-tos-text-view/
[Link 33]: ../mobile-engagement-how-tos-web-view/


 

<!--HONumber=47-->
