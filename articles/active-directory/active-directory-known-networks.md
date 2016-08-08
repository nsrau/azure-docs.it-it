<properties 
	pageTitle="Reti note | Microsoft Azure" 
	description="Configurando le reti note si evita che indirizzi IP di proprietà dell'organizzazione vengano inclusi nei report Accessi da più aree geografiche e Accessi da indirizzi IP con attività sospetta." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="femila"  
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/20/2016" 
	ms.author="markvi"/>

# Reti note


È possibile usare i report di utilizzo e accesso di Azure Active Directory per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore di directory può stabilire meglio dove potrebbero esserci possibili rischi per la sicurezza in modo da poterne pianificare adeguatamente la riduzione.

È possibile che i report "*Accessi da più aree geografiche*" e "*Accessi da indirizzi IP con attività sospetta*" indichino erroneamente indirizzi IP che sono effettivamente di proprietà dell'organizzazione.

Questo può avvenire, ad esempio, nei casi seguenti:

- L'accesso di un utente dell'ufficio di Boston in modalità remota al data center di San Francisco attiva il report "Accessi da più aree geografiche"

- Un utente dell'organizzazione tenta di accedere più volte con una password non corretta, attivando il report "Accessi da indirizzi IP con attività sospetta"

Per impedire che casi come questi generino report sulla sicurezza fuorvianti, è necessario aggiungere gli intervalli di indirizzi IP noti all'elenco di indirizzi IP pubblici dell'organizzazione.


###Per aggiungere gli intervalli di indirizzi IP pubblici dell'organizzazione, seguire questa procedura: 

1.	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2.	Nel riquadro di sinistra fare clic su **Active Directory**. <br><br>![Funzionamento di Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-01.png)

3.	Selezionare la propria directory nella scheda **Directory**.

4.	Nel menu in alto fare clic su **Configura**. <br><br>![Funzionamento di Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-02.png)

5.	Nella scheda Configura passare agli **intervalli di indirizzi IP pubblici dell'organizzazione** <br><br>![Funzionamento di Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-03.png)

6.	Fare clic su **Aggiungi intervalli di indirizzi IP noti**.

7.	Aggiungere gli intervalli di indirizzi nella finestra di dialogo visualizzata e quindi fare clic sul segno di spunta al termine dell'operazione. <br><br>![Funzionamento di Cloud App Discovery](./media/active-directory-known-networks/known-netwoks-04.png)


**Risorse aggiuntive**


* [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md)
* [Accessi da indirizzi IP con attività sospetta](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Accessi da più aree geografiche](active-directory-reporting-sign-ins-from-multiple-geographies.md)

<!---HONumber=AcomDC_0727_2016-->