<properties
	pageTitle="Anteprima di Azure Active Directory B2C: attributi personalizzati | Microsoft Azure"
	description="Come utilizzare gli attributi personalizzati in Azure Active Directory B2C per raccogliere informazioni sugli utenti"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="swkrish"/>

#  Anteprima di Azure Active Directory B2C: utilizzare attributi personalizzati per raccogliere informazioni sugli utenti

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

La directory B2C Azure Active Directory (AD) viene fornita con un set predefinito di attributi. ad esempio nome, cognome, città, codice postale, e così via. Tuttavia, ogni applicazione per consumatori ha dei requisiti specifici su quali informazioni (attributi) desiderano raccogliere dagli utenti. Azure AD B2C consente di estendere la directory (in particolare, di estendere il set di attributi archiviati in ogni account utente). È possibile creare attributi personalizzati sul [portale di anteprima di Azure](https://portal.azure.com/) e utilizzarli nei criteri per l'abbonamento, come illustrato di seguito. È inoltre possibile la lettura e la scrittura di questi attributi utilizzando l'API Azure AD Graph come mostrato [qui](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]Gli attributi personalizzati utilizzano [Estensioni dello schema della Directory di Graph API di Azure AD](https://msdn.microsoft.com/library/azure/dn720459.aspx) dietro le quinte.

## Come creare un attributo personalizzato

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)..
2. Fare clic su **Attributi utente**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per l'attributo personalizzato (ad esempio, "ShoeSize") e, facoltativamente, una **Descrizione**. Fare clic su **Crea**. L'operazione è completata.

    > [AZURE.NOTE]Solo la "stringa" **Tipo di dati** è attualmente disponibile. Si aggiungeranno più tipi di dati (DateTime, Integer e così via) in futuro.

L'attributo personalizzato è ora disponibile nell'elenco di **Attributi utente** e per l'utilizzo nei criteri per l'abbonamento.

## Come utilizzare un attributo personalizzato nei criteri di iscrizione

1. Passare al pannello delle funzionalità B2C nel [portale di anteprima di Azure](htts://portal.azure.com/). Leggere [qui](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) per sapere come eseguire questa operazione.
2. Fare clic su **Criteri di iscrizione**.
3. Aprire il criterio di iscrizione (ad esempio, "B2C\_1\_SiUp") facendo clic su di esso. Fare clic su **Modifica** nella parte superiore del pannello.
4. Fare clic su **Attributi iscrizione** e selezionare l'attributo personalizzato (ad esempio, "ShoeSize"). Fare clic su **OK**.
5. Fare clic su **Attestazioni applicazione** e selezionare l'attributo personalizzato. Fare clic su **OK**. 
6. Fare clic su **Salva** nella parte superiore del pannello. L'operazione è completata.

È possibile utilizzare la funzionalità "Esegui ora" nei criteri per verificare l'esperienza utente. Ora si dovrebbe vedere "ShoeSize" nell'elenco di attributi che vengono raccolti durante l'iscrizione dell’utente e visualizzarlo nel token inviato all'applicazione.

<!---HONumber=Sept15_HO4-->