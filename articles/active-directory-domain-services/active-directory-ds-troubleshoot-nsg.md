---
title: 'Azure Active Directory Domain Services: Risoluzione dei problemi della configurazione dei gruppi di sicurezza di rete | Microsoft Docs'
description: Risoluzione dei problemi relativi alla configurazione del gruppo di sicurezza di rete di Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 503e52266c1c6be71e60a751c40ef0a54f0d9b12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416425"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Risolvere i problemi di configurazione di rete non valida per il dominio gestito
Questo articolo consente di individuare e risolvere gli errori di configurazione relativi alla rete che causano il messaggio di avviso seguente:

## <a name="alert-aadds104-network-error"></a>Avviso AADDS104: Errore di rete
**Messaggio di avviso:** *Microsoft non riesce a raggiungere i controller di dominio per questo dominio gestito. È possibile che questo problema si verifichi se un gruppo di sicurezza di rete configurato sulla rete virtuale impedisce l'accesso al dominio gestito oppure se è presente una route definita dall'utente che blocca il traffico in ingresso da Internet.*

Le configurazioni di NSG non validi sono la causa più comune degli errori di rete per Azure AD Domain Services. Il gruppo di sicurezza di rete (NSG) configurato per la rete virtuale deve consentire l'accesso a [porte specifiche](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services). Se queste porte sono bloccate, Microsoft non può monitorare o aggiornare il dominio gestito. Inoltre, si verificano ripercussioni sulla sincronizzazione tra la directory Azure AD e il dominio gestito. Durante la creazione dell'NSG, tenere aperte queste porte per evitare possibili interruzioni del servizio.

### <a name="checking-your-nsg-for-compliance"></a>Controllo della conformità del gruppo di sicurezza di rete

1. Passare alla pagina [Gruppi di sicurezza di rete](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) nel portale di Azure
2. Dalla tabella scegliere l'NSG associato alla subnet in cui è abilitato il dominio gestito.
3. In **Impostazioni** nel pannello a sinistra, fare clic su **Regole di sicurezza in ingresso**
4. Esaminare le regole applicate e identificare quali regole bloccano l'accesso a [queste porte](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)
5. Modificare il gruppo di sicurezza di rete per garantire la conformità eliminando la regola, aggiungendo una regola o creando un gruppo di sicurezza di rete completamente nuovo. La procedura per [aggiungere una regola](#add-a-rule-to-a-network-security-group-using-the-azure-portal) o creare un nuovo gruppo sicurezza di rete conforme è riportata di seguito

## <a name="sample-nsg"></a>Esempio di NSG
La tabella seguente illustra un esempio di gruppo di sicurezza di rete finalizzato a proteggere il dominio gestito e consentire al contempo a Microsoft di monitorare, gestire e aggiornare le informazioni.

![esempio di NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services richiede un accesso in uscita senza restrizioni dalla rete virtuale. Si consiglia di non creare regole NSG aggiuntive che limitano l'accesso in uscita per la rete virtuale.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Aggiungere una regola a un gruppo di sicurezza di rete tramite il portale di Azure
Se non si desidera usare PowerShell, è possibile aggiungere manualmente delle regole singole per NSG tramite il portale di Azure. Per creare regole nel gruppo di sicurezza di rete, svolgere i passaggi seguenti:

1. Passare alla pagina [Gruppi di sicurezza di rete](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) nel portale di Azure.
2. Dalla tabella scegliere l'NSG associato alla subnet in cui è abilitato il dominio gestito.
3. In **Impostazioni** nel pannello a sinistra fare clic su **Regole di sicurezza in ingresso** o **Regole di sicurezza in uscita**.
4. Creare la regola facendo clic su **Aggiungi** e inserire le informazioni. Fare clic su **OK**.
5. Verificare che la regola sia stata creata cercandola nella tabella delle regole.


## <a name="need-help"></a>Richiesta di assistenza
Contattare il team di prodotto di Servizi di dominio Azure AD per [condividere commenti e suggerimenti o per chiedere supporto tecnico](active-directory-ds-contact-us.md).
