<properties
	pageTitle="Condivisione e controllo di accesso per i dashboard di Azure | Microsoft Azure"
	description="Informazioni su come gestire la condivisione e il controllo di accesso per i dashboard di Azure."
	services="azure-resource-manager,azure-portal"
    keywords="portale, condivisione, accesso"
	documentationCenter=""
	authors="adamabdelhamed"
	manager="dend"
	editor="dend"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="adamab"/>
    
# Condivisione e controllo di accesso per i dashboard di Azure

L'accesso alle informazioni visualizzate nella maggior parte dei riquadri del portale è regolato dal [Controllo degli accessi in base al ruolo](./active-directory/role-based-access-control-configure.md) di Azure. Per integrare facilmente i dashboard nell'ecosistema, tutti quelli pubblicati vengono implementati come risorse di Azure. Da un punto di vista del controllo di accesso, i dashboard non sono diversi da una macchina virtuale o da un account di archiviazione.

Di seguito è fornito un esempio. Si supponga di avere una sottoscrizione di Azure e che a diversi membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione. Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per risolvere un problema, ma non pubblicarle nel server. Hanno comunque la possibilità di creare una copia privata del dashboard per se stessi.

Si noti che i singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse per cui visualizzano i dati. Ciò significa che è possibile progettare un dashboard più ampiamente condivisibile, continuando comunque a proteggere i dati nei singoli riquadri.

<!---HONumber=AcomDC_0622_2016-->