---
title: Strumento per l'analisi di simulazione dell'accesso condizionale di Azure Active Directory - Anteprima | Microsoft Docs
description: Informazioni su come testare la configurazione dei criteri di accesso condizionale di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 19ebb30164eee8e03a3cd8f18b6d575c6eee5438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Strumento per l'analisi di simulazione dell'accesso condizionale di Azure Active Directory - Anteprima

L'[accesso condizionale](active-directory-conditional-access-azure-portal.md) è una funzionalità di Azure Active Directory (Azure AD) che consente di controllare l'accesso degli utenti autorizzati alle app cloud. Per sapere quale sarà il comportamento dei criteri di accesso condizionale nell'ambiente in uso, è possibile usare lo **strumento per l'analisi di simulazione dell'accesso condizionale**.

Questo articolo spiega come usare questo strumento per testare i criteri di accesso condizionale.

## <a name="what-it-is"></a>Che cos'è

Lo **strumento per l'analisi di simulazione dell'accesso condizionale** consente di comprendere come agiscono i criteri di accesso condizionale nell'ambiente in uso. Anziché testare i criteri eseguendo più accessi in modo manuale, questo strumento consente di valutare un accesso simulato di un utente. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione. Questo report contiene non solo i criteri di accesso condizionale applicati, ma anche gli eventuali [criteri classici](active-directory-conditional-access-migration.md#classic-policies) esistenti.    

Lo strumento per l'analisi di simulazione offre anche la possibilità di stabilire rapidamente i criteri che si applicano a un utente specifico. Queste informazioni possono essere usate, ad esempio, se è necessario risolvere un problema.  

## <a name="how-it-works"></a>Funzionamento

Nello **strumento per l'analisi di simulazione dell'accesso condizionale** è prima necessario configurare le impostazioni dello scenario di accesso che si vuole simulare. Tali impostazioni includono:

- L'utente da testare 

- Le app cloud a cui l'utente proverà ad accedere

- Le condizioni in cui viene eseguito l'accesso alle app cloud configurate
     
Come passaggio successivo, è possibile avviare una simulazione per valutare le impostazioni. Una valutazione prende in esame solo i criteri abilitati.


Al termine della valutazione, lo strumento genera un report dei criteri interessati.


## <a name="running-the-tool"></a>Esecuzione dello strumento

Lo strumento per l'**analisi di simulazione** è disponibile nella pagina **[Accesso condizionale - Criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** del portale di Azure.

Per avviare lo strumento, fare clic su **Analisi di simulazione** sulla barra degli strumenti in cima all'elenco dei criteri.

![Analisi di simulazione](./media/active-directory-conditional-access-whatif/01.png)

Prima di eseguire una valutazione, è necessario configurare le impostazioni.

## <a name="settings"></a>Impostazioni

Questa sezione fornisce informazioni sulle impostazioni della simulazione.

![Analisi di simulazione](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>Utente

È possibile selezionare un solo utente. Questo campo è l'unico obbligatorio.

### <a name="cloud-apps"></a>App cloud

Il valore predefinito di questa impostazione è **Tutte le app cloud**. Con l'impostazione predefinita viene eseguita una valutazione di tutti i criteri disponibili nell'ambiente in uso. È possibile restringere l'ambito di valutazione ai criteri che interessano app cloud specifiche.


### <a name="ip-address"></a>Indirizzo IP

L'indirizzo IP è un singolo indirizzo IPv4 necessario per simulare la [condizione di posizione](active-directory-conditional-access-locations.md). Rappresenta l'indirizzo per Internet del dispositivo usato dall'utente per eseguire l'accesso. È possibile verificare l'indirizzo IP di un dispositivo accedendo, ad esempio, al sito Web [What is my IP address](https://whatismyipaddress.com).    

### <a name="device-platforms"></a>Piattaforme del dispositivo

Questa impostazione simula la [condizione di piattaforme del dispositivo](active-directory-conditional-access-conditions.md#device-platforms) ed equivale all'opzione **Tutte le piattaforme (incluse quelle non supportate)**. 
### <a name="client-apps"></a>App client

Questa impostazione simula la [condizione di app client](active-directory-conditional-access-conditions.md#client-apps).
Per impostazione predefinita, esegue la valutazione di tutti i criteri per i quali è selezionato **Browser** o **App per dispositivi mobili e client desktop** o sono selezionate entrambe le opzioni. Rileva anche i criteri che applicano **Exchange ActiveSync (EAS)**. Per restringere l'ambito di questa impostazione, selezionare:

- **Browser** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **Browser**. 

- **App per dispositivi mobili e client desktop** per valutare tutti i criteri per i quali è selezionata almeno l'opzione **App per dispositivi mobili e client desktop**. 


### <a name="sign-in-risk"></a>Rischio di accesso

Questa impostazione simula la [condizione di rischio di accesso](active-directory-conditional-access-conditions.md#sign-in-risk).   


## <a name="evaluation"></a>Versione di valutazione 

Per avviare una valutazione, fare clic su **Analisi di simulazione**. Al termine della valutazione, viene generato un report contenente gli elementi seguenti: 

![Analisi di simulazione](./media/active-directory-conditional-access-whatif/03.png)

- Un indicatore che segnala l'eventuale presenza di criteri classici nell'ambiente in uso
- I criteri che si applicano all'utente
- I criteri che non si applicano all'utente


Se per le app cloud selezionate sono presenti [criteri classici](active-directory-conditional-access-migration.md#classic-policies), viene visualizzato un indicatore. Facendo clic sull'indicatore, l'utente viene reindirizzato alla pagina relativa ai criteri classici. In questa pagina è possibile semplicemente disabilitare un criterio classico o eseguirne la migrazione. Chiudendo la pagina è possibile tornare ai risultati della valutazione.

Nell'elenco dei criteri che si applicano all'utente selezionato è possibile trovare anche un elenco di [controlli di concessione](active-directory-conditional-access-controls.md#grant-controls) e controlli di [sessione](active-directory-conditional-access-controls.md#session-controls) che l'utente deve soddisfare.

Nell'elenco dei criteri che non si applicano all'utente è possibile trovare anche i motivi di tale inapplicabilità. Per ogni criterio elencato, il motivo rappresenta la prima condizione che non è stata soddisfatta. Un possibile motivo per cui un criterio non viene applicato è che sia stato disabilitato. In questo caso, il criterio non viene ulteriormente valutato.   



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

- Per informazioni sulla migrazione dei criteri classici, vedere [Eseguire la migrazione dei criteri classici nel portale di Azure](active-directory-conditional-access-migration.md).  
