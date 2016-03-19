<properties
   pageTitle="Azure Privileged Identity Management: ruoli"
   description="Informazioni sui ruoli utilizzati per le identità con privilegi con l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: ruoli

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Ruoli da Azure Active Directory, Office 365 e altre origini

Azure Privileged Identity Management (PIM) usa i ruoli seguenti come ruoli di amministratore predefiniti:

- Amministratore globale
- Amministratore fatturazione
- Amministratore del servizio
- Amministratore utenti
- Amministratore password

Per altre informazioni sui ruoli di Office 365, Exchange Online, SharePoint Online e Skype for Business, vedere [Assegnazione di ruoli di amministrazione in Office 365](https://support.office.com/it-IT/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=it-IT&rs=it-IT&ad=US).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Ruoli utente e accesso
> [AZURE.NOTE]Per accedere ad Azure PIM, un utente deve avere una licenza per Azure.

## Assegnazione di una licenza a un utente in Azure AD

> [AZURE.NOTE] L'opzione di licenza verrà visualizzata solo se le licenze esistono effettivamente per questa sottoscrizione.

1. Con un account di amministratore globale o un account di coamministratore, accedere a [http://manage.windowsazure.com](http://manage.windowsazure.com).
2. Fare clic su **Tutti gli elementi** nel menu principale.
3. Selezionare la directory con cui si desidera lavorare e a cui sono associate licenze.
4. Fare clic su **Licenze**. Verrà visualizzato l'elenco delle licenze disponibili.
5. Fare clic sul piano di licenza che contiene le licenze che si desidera distribuire.
6. Fare clic su **Assegna utenti**.
7. Selezionare l'utente a cui si desidera assegnare una licenza.
8. Scegliere il pulsante **Assegna**. L'utente ora può accedere ad Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->