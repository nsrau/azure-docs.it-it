<properties
   pageTitle="Azure Privileged Identity Management: Come richiedere l'MFA"
   description="Informazioni su come richiedere l’MFA (Multi-Factor Authentication) per identità con privilegi con l'estensione Azure Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: Come richiedere l'MFA

È consigliabile richiedere la Multi-Factor Authentication per tutti gli amministratori.

##Richiedere l'MFA in Azure Privileged Identity Management
Quando si accede come amministratore PIM, si riceveranno avvisi che suggeriscono che gli account con privilegi devono richiedere la Multi-Factor Authentication (MFA). Fare clic sull'avviso di sicurezza nel dashboard PIM e verrà aperto un nuovo pannello con un elenco degli account amministratore che devono richiedere l'MFA. È possibile richiedere il servizio MFA selezionando più ruoli e quindi facendo clic sul pulsante **Correggi** oppure è possibile fare clic sui puntini di sospensione accanto ai singoli ruoli e quindi fare clic sul pulsante **Correggi**.

È anche possibile modificare il requisito di MFA per un ruolo specifico facendo clic sul ruolo nella sezione Ruoli del dashboard e abilitando il servizio MFA per tale ruolo facendo clic su **Impostazioni** nel pannello del ruolo e quindi selezionando l'opzione **Abilita** in Multi-Factor Authentication.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->