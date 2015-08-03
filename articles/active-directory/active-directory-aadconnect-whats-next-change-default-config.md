<properties 
	pageTitle="Modificare la configurazione predefinita di Azure AD Connect" 
	description="Informazioni su come modificare la configurazione predefinita per Azure AD Connect." 
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

# Modifica della configurazione predefinita di Azure AD Connect 


Nella maggior parte dei casi, la configurazione predefinita di Azure AD Connect è sufficiente per estendere le directory locali nel cloud. Tuttavia, in alcuni casi può essere necessario modificarla per adattarla alla logica di business dell'organizzazione. Benché in questi casi sia possibile modificare la configurazione predefinita, è bene tenere presenti alcuni aspetti.

Quando è necessario modificare la configurazione predefinita, eseguire le operazioni seguenti:

- Evitare di modificare un flusso di attributi di una regola di sincronizzazione predefinita. Creare invece una nuova regola di sincronizzazione con una priorità superiore (valore numerico inferiore) che contenga il flusso di attributi necessario.
- Esportare le regole di sincronizzazione personalizzate usando l'Editor regole di sincronizzazione. In questo modo, viene fornito uno script di PowerShell che è possibile usare per ricrearle facilmente nel caso di uno scenario di ripristino di emergenza.
- Se è necessario modificare l'ambito o l'impostazione di unione in una regola di sincronizzazione predefinita, documentare questa necessità e riapplicare la modifica dopo l'aggiornamento a una versione più recente di Azure AD Connect. 

<!---HONumber=July15_HO4-->