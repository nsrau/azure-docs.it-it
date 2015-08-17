

<properties 
	pageTitle="Uso del provisioning dichiarativo di Azure AD Connect" 
	description="Informazioni su come utilizzare il provisioning dichiarativo di Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Provisioning dichiarativo di Azure AD Connect


## Uso del provisioning dichiarativo 
Il provisioning dichiarativo è un tipo di provisioning "senza codice" che può essere impostato e configurato usando l'Editor regole di sincronizzazione. L'editor può essere usato per configurare e creare regole di provisioning personalizzate.

Una parte essenziale del provisioning dichiarativo è il linguaggio delle espressioni usato nei flussi di attributi. Il linguaggio usato è un subset di Microsoft® Visual Basic®, Applications Edition (VBA). Questo linguaggio viene usato in Microsoft Office e verrà riconosciuto anche dagli utenti con esperienza in VBScript. Il linguaggio delle espressioni di provisioning dichiarativo usa solo funzioni e non è un linguaggio strutturato, né include metodi o istruzioni. Le funzioni verranno invece annidate per esprimere flussi di programmi.

Per altre informazioni sul linguaggio delle espressioni, vedere [Informazioni sulle espressioni di provisioning dichiarativo](https://msdn.microsoft.com/library/azure/dn801048.aspx).

<!---HONumber=August15_HO6-->