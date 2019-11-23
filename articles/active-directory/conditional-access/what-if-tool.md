---
title: The Conditional Access What If tool - Azure Active Directory
description: Learn how you can understand the impact of your Conditional Access policies on your environment.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5846934a8ad8455ca375b4bc54fc46d45aba1cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379988"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Troubleshoot using the What If tool in Conditional Access

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active Directory (Azure AD) that enables you to control how authorized users access your cloud apps. How do you know what to expect from the Conditional Access policies in your environment? To answer this question, you can use the **Conditional Access What If tool**.

This article explains how you can use this tool to test your Conditional Access policies.

## <a name="what-it-is"></a>Che cos'è

The **Conditional Access What If policy tool** allows you to understand the impact of your Conditional Access policies on your environment. Anziché testare i criteri eseguendo più accessi in modo manuale, questo strumento ti permette di valutare un accesso simulato di un utente. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione. The report does not only list the applied Conditional Access policies but also [classic policies](policy-migration.md#classic-policies) if they exist.    

The **What If** tool provides a way to quickly determine the policies that apply to a specific user. Queste informazioni possono essere usate, ad esempio, se è necessario risolvere un problema.    

## <a name="how-it-works"></a>Come funziona

In the **Conditional Access What If tool**, you first need to configure the settings of the sign-in scenario you want to simulate. Tali impostazioni includono:

- L'utente da testare 
- Le app cloud a cui l'utente proverà ad accedere
- Le condizioni in cui viene eseguito l'accesso alle app cloud configurate
     
Come passaggio successivo, è possibile avviare una simulazione per valutare le impostazioni. Una valutazione prende in esame solo i criteri abilitati.

Al termine della valutazione, lo strumento genera un report dei criteri interessati.

## <a name="running-the-tool"></a>Esecuzione dello strumento

You can find the **What If** tool on the **[Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** page in the Azure portal.

To start the tool, in the toolbar on top of the list of policies, click **What If**.

![What If](./media/what-if-tool/01.png)

Prima di eseguire una valutazione, è necessario configurare le impostazioni.

## <a name="settings"></a>Impostazioni

Questa sezione fornisce informazioni sulle impostazioni della simulazione.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Utente

È possibile selezionare un solo utente. Questo campo è l'unico obbligatorio.

### <a name="cloud-apps"></a>App cloud

Il valore predefinito di questa impostazione è **Tutte le app cloud**. Con l'impostazione predefinita viene eseguita una valutazione di tutti i criteri disponibili nell'ambiente in uso. È possibile restringere l'ambito di valutazione ai criteri che interessano app cloud specifiche.

### <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP è un singolo indirizzo IPv4 necessario per simulare la [condizione di posizione](location-condition.md). Rappresenta l'indirizzo per Internet del dispositivo usato dall'utente per eseguire l'accesso. È possibile verificare l'indirizzo IP di un dispositivo accedendo, ad esempio, al sito Web [What is my IP address](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Piattaforme del dispositivo

Questa impostazione simula la [condizione di piattaforme del dispositivo](conditions.md#device-platforms) ed equivale all'opzione **Tutte le piattaforme (incluse quelle non supportate)** . 

### <a name="client-apps"></a>App client

Questa impostazione simula la [condizione di app client](conditions.md#client-apps).
Per impostazione predefinita, esegue la valutazione di tutti i criteri per i quali è selezionato **Browser** o **App per dispositivi mobili e client desktop** o sono selezionate entrambe le opzioni. Rileva anche i criteri che applicano **Exchange ActiveSync (EAS)** . Per restringere l'ambito di questa impostazione, selezionare:

- **Browser** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **Browser**. 
- **App per dispositivi mobili e client desktop** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **App per dispositivi mobili e client desktop**. 

### <a name="sign-in-risk"></a>Rischio di accesso

Questa impostazione simula la [condizione di rischio di accesso](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Versione di valutazione 

You start an evaluation by clicking **What If**. Al termine della valutazione, viene generato un report contenente gli elementi seguenti: 

![What If](./media/what-if-tool/03.png)

- Un indicatore che segnala l'eventuale presenza di criteri classici nell'ambiente in uso
- I criteri che si applicano all'utente
- I criteri che non si applicano all'utente

Se per le app cloud selezionate sono presenti [criteri classici](policy-migration.md#classic-policies), viene visualizzato un indicatore. Facendo clic sull'indicatore, l'utente viene reindirizzato alla pagina relativa ai criteri classici. In questa pagina è possibile semplicemente disabilitare un criterio classico o eseguirne la migrazione. Chiudendo la pagina è possibile tornare ai risultati della valutazione.

Nell'elenco dei criteri che si applicano all'utente selezionato è possibile trovare anche un elenco di [controlli di concessione](controls.md#grant-controls) e controlli di [sessione](controls.md#session-controls) che l'utente deve soddisfare.

Nell'elenco dei criteri che non si applicano all'utente è possibile trovare anche i motivi di tale inapplicabilità. Per ogni criterio elencato, il motivo rappresenta la prima condizione che non è stata soddisfatta. Un possibile motivo per cui un criterio non viene applicato è che sia stato disabilitato. In questo caso, il criterio non viene ulteriormente valutato.   

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory](app-based-mfa.md).
- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale](best-practices.md) in Azure Active Directory. 
- Per informazioni sulla migrazione dei criteri classici, vedere [Eseguire la migrazione dei criteri classici nel portale di Azure](policy-migration.md).  
