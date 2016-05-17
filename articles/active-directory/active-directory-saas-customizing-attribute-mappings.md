<properties
	pageTitle="Personalizzazione dei mapping degli attributi | Microsoft Azure"
	description="Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2016"
	ms.author="markusvi"/>


# Personalizzazione dei mapping degli attributi


Microsoft Azure AD fornisce supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, Google Apps e altre. Se è abilitato il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di gestione di Azure controlla i relativi valori di attributo mediante una configurazione denominata "mapping degli attributi".

Esiste un set preconfigurato di mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. <br> È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. È infatti possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.

Nel portale di Azure AD è possibile accedere a questa funzionalità facendo clic su Attributi sulla barra degli strumenti di un'applicazione SaaS.

> [AZURE.NOTE] Il collegamento **Attributi** è disponibile solo se il provisioning degli utenti è abilitato per un'applicazione SaaS.


![Salesforce][1]


Quando si fa clic su Attributi sulla barra degli strumenti viene visualizzato l'elenco dei mapping correnti configurati per un'applicazione SaaS.

La schermata seguente mostra un esempio relativo a questa operazione:



![Salesforce][2]


Nell'esempio riportato sopra si noterà che l'attributo **firstName** di un oggetto gestito in Salesforce è popolato con il valore **givenName** dell'oggetto di Azure AD collegato.

Se si vuole personalizzare il mapping degli attributi o ripristinare la configurazione predefinita, è sufficiente clic sui pulsanti corrispondenti sulla barra degli strumenti nella parte inferiore di un'applicazione.


![Salesforce][3]


Alcuni mapping di attributi sono obbligatori per il corretto funzionamento di un'applicazione SaaS. I mapping di attributi correlati sono contrassegnati dal valore **Sì** per l'attributo **Obbligatorio** nella tabella degli attributi. Se un mapping di attributi è obbligatorio, non può essere eliminato. In questo caso l'opzione **Elimina** non è disponibile.

Per modificare un mapping degli attributi esistente, è sufficiente selezionarlo e fare clic su **Modifica**. Verrà visualizzata una finestra di dialogo che permette di modificare il mapping degli attributi selezionato.


![Modifica dei mapping degli attributi][4]



## Informazioni sui tipi di mapping degli attributi


I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti. Sono supportati quattro diversi tipi di mapping:

- **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.


- **Costante:** l'attributo di destinazione viene popolato con una stringa specificata.


- **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script. Per informazioni dettagliate, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Se l'attributo di destinazione viene lasciato vuoto, verrà però popolato con il valore predefinito specificato.



In aggiunta a questi quattro tipi base di mapping di attributi, i mapping di attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito**. L'assegnazione di un valore predefinito garantisce che un attributo di destinazione venga popolato con un valore nel caso in cui non sia presente un valore né in Azure AD, né nell'oggetto di destinazione.

Microsoft Azure AD offre un'implementazione estremamente efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti. L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Questo dipende dal fatto che un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti. Per questo motivo è consigliabile ridurre al minimo il numero di modifiche consecutive ai mapping degli attributi.


##Articoli correlati

- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](active-directory-saas-app-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
- [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

<!---HONumber=AcomDC_0504_2016-->