---
title: Pianificare la distribuzione del dispositivo Azure Active Directory
description: Scegliere le strategie di integrazione del dispositivo Azure AD che soddisfano le esigenze dell'organizzazione.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d3e3c174ad9a3372df084cac9eb67270779298
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563897"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Pianificare la distribuzione del dispositivo Azure Active Directory

Questo articolo consente di valutare i metodi per integrare il dispositivo con Azure AD, scegliere il piano di implementazione e fornisce collegamenti chiave agli strumenti di gestione dei dispositivi supportati.

Il panorama dei dispositivi da cui gli utenti si iscrivono si espande. Le organizzazioni possono fornire desktop, portatili, telefoni, tablet e altri dispositivi. Gli utenti possono riunire la propria matrice di dispositivi e accedere a informazioni da diverse posizioni. In questo ambiente, il processo come amministratore è quello di proteggere le risorse dell'organizzazione in tutti i dispositivi.

Azure Active Directory (Azure AD) consente all'organizzazione di raggiungere questi obiettivi con la gestione delle identità dei dispositivi. È ora possibile ottenere i dispositivi in Azure AD e controllarli da una posizione centrale nel [portale di Azure](https://portal.azure.com/). In questo modo si ottiene un'esperienza unificata, una sicurezza migliorata e si riduce il tempo necessario per configurare un nuovo dispositivo.

Sono disponibili diversi metodi per integrare i dispositivi in Azure AD:

* È possibile [registrare i dispositivi](concept-azure-ad-register.md) con Azure ad

* [Aggiungere dispositivi](concept-azure-ad-join.md) a Azure ad (solo cloud) o

* [Creare un join di Azure ad ibrido](concept-azure-ad-join-hybrid.md) tra dispositivi nel Active Directory locale e Azure ad. 

## <a name="learn"></a>Learn

Prima di iniziare, assicurarsi di avere familiarità con la [panoramica sulla gestione delle identità dei dispositivi](overview.md).

### <a name="benefits"></a>Vantaggi

I vantaggi principali per fornire ai dispositivi un'identità Azure AD:

* Aumentare la produttività: con Azure AD, gli utenti possono eseguire l' [accesso Single Sign-on (SSO)](./azuread-join-sso.md) alle risorse locali e cloud, consentendo loro di essere produttivi ovunque si trovino.

* Aumentare la sicurezza: i dispositivi Azure AD consentono di applicare i [criteri di accesso condizionale](../conditional-access/require-managed-devices.md) alle risorse in base all'identità del dispositivo o dell'utente. I criteri CA possono offrire protezione aggiuntiva usando [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md). L'aggiunta di un dispositivo a Azure AD è un prerequisito per aumentare la sicurezza con una strategia di [autenticazione senza password](../authentication/concept-authentication-passwordless.md) .

* Migliorare l'esperienza utente: con le identità del dispositivo in Azure AD, è possibile fornire agli utenti un accesso facile alle risorse basate sul cloud dell'organizzazione da dispositivi personali e aziendali. Gli amministratori possono abilitare [Enterprise state roaming](enterprise-state-roaming-overview.md) per un'esperienza unificata in tutti i dispositivi Windows.

* Semplificare la distribuzione e la gestione: la gestione delle identità dei dispositivi semplifica il processo di inserimento dei dispositivi Azure AD con [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), il [provisioning in blocco](/mem/intune/enrollment/windows-bulk-enroll)e la funzionalità [self-service](../user-help/user-help-join-device-on-network.md), ovvero la configurazione guidata. È possibile gestire questi dispositivi con strumenti di gestione di dispositivi mobili (MDM) come [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)e le relative identità in [portale di Azure](https://portal.azure.com/).

### <a name="training-resources"></a>Risorse di formazione

Video:  [accesso condizionale con controlli dispositivo](https://youtu.be/NcONUf-jeS4)

Domande frequenti: [Azure ad domande frequenti sulla gestione dei dispositivi](faq.md)  e [Impostazioni e domande frequenti sui dati in roaming](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici non hanno successo, in genere la causa è legata alle diverse aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans) e che i ruoli di stakeholder nel progetto siano ben noti. 

Per questo piano, aggiungere gli stakeholder seguenti all'elenco:

| Ruolo| Descrizione |
| - | - |
| Amministratore del dispositivo| Un rappresentante del team del dispositivo che può verificare che il piano soddisfi i requisiti del dispositivo dell'organizzazione. |
| Amministratore di rete| Rappresentante del team di rete che può assicurarsi di soddisfare i requisiti di rete. |
| Team di gestione dei dispositivi| Team che gestisce l'inventario dei dispositivi. |
| Team amministratori specifici del sistema operativo| Team che supportano e gestiscono versioni specifiche del sistema operativo. Ad esempio, potrebbe essere presente un team con stato attivo per Mac o iOS. |

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunica in modo proattivo con gli utenti su come cambierà l'esperienza, quando verrà modificata, e su come ottenere supporto in caso di problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile che la configurazione iniziale del metodo di integrazione si trovi in un ambiente di test o con un piccolo gruppo di dispositivi di test. Vedere [Procedure consigliate per un progetto pilota](../fundamentals/active-directory-deployment-plans.md).

Azure AD ibrido distribuzione join è semplice ed è il 100% dell'attività di un amministratore senza l'intervento dell'utente finale necessario. Potrebbe essere necessario eseguire una [convalida controllata del join Azure ad ibrido prima di](hybrid-azuread-join-control.md) abilitarlo in tutta l'organizzazione contemporaneamente.

## <a name="choose-your-integration-methods"></a>Scegliere i metodi di integrazione

L'organizzazione può usare più metodi di integrazione dei dispositivi in un singolo tenant Azure AD. L'obiettivo è scegliere il metodo o i metodi adatti per gestire i dispositivi in modo sicuro in Azure AD. Sono disponibili molti parametri che determinano questa decisione, tra cui la proprietà, i tipi di dispositivi, i destinatari primari e l'infrastruttura dell'organizzazione.

Le informazioni seguenti possono essere utili per decidere quali metodi di integrazione utilizzare.

### <a name="decision-tree-for-devices-integration"></a>Albero delle decisioni per l'integrazione dei dispositivi

Utilizzare questo albero per determinare le opzioni per i dispositivi di proprietà dell'organizzazione. 

> [!NOTE]
> Gli scenari personali o Bring Your Own Device (BYOD) non sono illustrati in questo diagramma. Hanno sempre come risultato la registrazione Azure AD.

 ![Albero delle decisioni](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Matrice di confronto

i dispositivi iOS e Android possono essere registrati solo Azure AD. La tabella seguente presenta considerazioni di alto livello per i dispositivi client Windows. Utilizzarlo come panoramica, quindi esplorare in dettaglio i diversi metodi di integrazione.

| Considerazioni | Registrazione in Azure AD| Aggiunta ad Azure AD| Aggiunta ad Azure AD ibrido |
| - | - | - | - |
| **Sistemi operativi client**| | |  |
| Dispositivi Windows 10| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Dispositivi Windows di livello inferiore (Windows 8.1 o Windows 7)| | | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
|**Opzioni di accesso**| | |  |
| Credenziali locali dell'utente finale| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| |  |
| Password| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| PIN per i dispositivi| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello for Business| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Chiavi di sicurezza di FIDO 2,0| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| App Microsoft Authenticator (con password)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
|**Funzionalità principali**| | |  |
| Da SSO a risorse cloud| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Accesso SSO alle risorse locali| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Accesso condizionale <br> (Richiedi che i dispositivi siano contrassegnati come conformi) <br> (Deve essere gestito da MDM)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)|![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
Accesso condizionale <br>(Richiedi dispositivi ibridi Azure AD aggiunti)| | | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)
| Reimpostazione della password self-service dalla schermata di accesso di Windows| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Ripristino del PIN di Windows Hello| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |
| Enterprise state roaming tra dispositivi| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Registrazione Azure AD 

I dispositivi registrati vengono spesso gestiti con [Microsoft Intune](/mem/intune/enrollment/device-enrollment). I dispositivi vengono registrati in Intune in diversi modi, a seconda del sistema operativo. 

Azure AD dispositivi registrati offrono il supporto per i dispositivi BYOD (Bring Your Own Device) e i dispositivi di proprietà dell'azienda per l'accesso SSO alle risorse cloud. L'accesso alle risorse è basato sui criteri di Azure AD [CA](../conditional-access/require-managed-devices.md) applicati al dispositivo e all'utente.

### <a name="registering-devices"></a>Registrazione dei dispositivi

I dispositivi registrati vengono spesso gestiti con [Microsoft Intune](/mem/intune/enrollment/device-enrollment). I dispositivi vengono registrati in Intune in diversi modi, a seconda del sistema operativo. 

BYOD e il dispositivo mobile di proprietà dell'azienda sono registrati dagli utenti che installano l'app portale aziendale.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Se la registrazione dei dispositivi è l'opzione migliore per l'organizzazione, vedere le risorse seguenti:

* Questa panoramica di [Azure ad dispositivi registrati](concept-azure-ad-register.md).

* Questa documentazione per l'utente finale [registra il dispositivo personale nella rete dell'organizzazione](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Aggiunta ad Azure AD

Azure AD join consente di passare a un modello cloud-First con Windows. Offre un'ottima base per la pianificazione della modernizzazione della gestione dei dispositivi e per la riduzione dei costi IT relativi ai dispositivi. Azure AD join funziona solo con i dispositivi Windows 10. Considerarlo come la prima scelta per i nuovi dispositivi.

Tuttavia, [Azure ad i dispositivi aggiunti possono SSO alle risorse locali](azuread-join-sso.md) quando si trovano nella rete dell'organizzazione, possono eseguire l'autenticazione in server locali come file, stampa e altre applicazioni.

Se si tratta dell'opzione migliore per l'organizzazione, vedere le risorse seguenti:

* Questa panoramica dei [dispositivi Azure ad aggiunti](concept-azure-ad-join.md).

* Acquisire familiarità con il [piano di implementazione di Azure ad join](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Provisioning Azure AD aggiunta ai dispositivi

Per eseguire il provisioning di Azure AD join, sono disponibili gli approcci seguenti:

* Self-Service: [esperienza di prima esecuzione di Windows 10](azuread-joined-devices-frx.md)

Se nel dispositivo è installato Windows 10 Professional o Windows 10 Enterprise, per impostazione predefinita viene avviato il processo di installazione dei dispositivi di proprietà dell'azienda.

* [Esperienza di Windows predefinita (configurazione guidata) o impostazioni di Windows](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Registrazione in blocco](/mem/intune/enrollment/windows-bulk-enroll)

Scegliere la procedura di distribuzione dopo un attento [confronto di questi approcci](azureadjoin-plan.md).

È possibile determinare che Azure AD join è la soluzione migliore per un dispositivo e che il dispositivo potrebbe già trovarsi in uno stato diverso. Ecco le considerazioni sull'aggiornamento.

| Stato corrente del dispositivo| Stato del dispositivo desiderato| Procedure |
| - | - | - |
| Aggiunta a un dominio locale| Aggiunta ad Azure AD| Separare il dispositivo dal dominio locale prima di unirlo a Azure AD |
| Join ibrido ad Azure AD| Aggiunta ad Azure AD| Separare il dispositivo dal dominio locale e da Azure AD prima di unirlo a Azure AD |
| Registrazione in Azure AD| Aggiunta ad Azure AD| Annulla la registrazione del dispositivo prima di unirlo a Azure AD |


## <a name="hybrid-azure-ad-join"></a>Aggiunta ad Azure AD ibrido

Se si dispone di un ambiente di Active Directory locale e si desidera aggiungere i computer appartenenti a un dominio di Active Directory a Azure AD, è possibile eseguire questa operazione con il join di Azure AD ibrido. Supporta un' [ampia gamma di dispositivi Windows](hybrid-azuread-join-plan.md), inclusi i dispositivi Windows correnti e Windows di livello inferiore.

La maggior parte delle organizzazioni dispone già di dispositivi aggiunti a un dominio e li gestisce tramite Criteri di gruppo o System Center Configuration Manager (SCCM). In tal caso, è consigliabile configurare Hybrid Azure AD join per iniziare a ottenere i vantaggi sfruttando gli investimenti esistenti.

Se Hybrid Azure AD join è l'opzione migliore per l'organizzazione, vedere le risorse seguenti:

* Questa panoramica dei [dispositivi Azure ad ibrido aggiunti](concept-azure-ad-join-hybrid.md).

* Acquisire familiarità con il piano di [implementazione di Azure ad ibrido join](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Provisioning di Azure AD ibrido di join ai dispositivi

[Esaminare l'infrastruttura di identità](hybrid-azuread-join-plan.md). Azure AD Connect fornisce una procedura guidata per configurare il join di Azure AD ibrido per:

* [Domini federati](hybrid-azuread-join-federated-domains.md)

* [Domini gestiti](hybrid-azuread-join-managed-domains.md)

Se non è possibile installare la versione richiesta di Azure AD Connect, vedere [come configurare manualmente Azure ad ibrido join](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Il dispositivo Windows 10 aggiunto al dominio locale tenta di eseguire automaticamente il join a Azure AD per diventare Azure AD ibrido aggiunto per impostazione predefinita. Questa operazione avrà esito positivo solo se è stato configurato l'ambiente corretto. 

È possibile determinare che Aggiunta ad Azure AD ibrido è la soluzione migliore per un dispositivo e che il dispositivo potrebbe essere già in uno stato diverso. Ecco le considerazioni sull'aggiornamento.

| Stato corrente del dispositivo| Stato del dispositivo desiderato| Procedure |
| - | - | - |
| Aggiunta a un dominio locale| Join ibrido ad Azure AD| Usare Azure AD connettersi o AD FS per accedere ad Azure |
| Gruppo di lavoro locale aggiunto o nuovo| Join ibrido ad Azure AD| Supportato con [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot). In caso contrario, il dispositivo deve essere aggiunto a un dominio locale prima di Aggiunta ad Azure AD ibrido |
| Aggiunta ad Azure AD| Join ibrido ad Azure AD| Separare da Azure AD, che lo inserisce nel gruppo di lavoro locale o nel nuovo stato. |
| Azure AD registrato| Join ibrido ad Azure AD| Dipende dalla versione di Windows. [Vedere le considerazioni seguenti](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Gestire i dispositivi

Dopo aver registrato o aggiunto i dispositivi a Azure AD, usare il [portale di Azure](https://portal.azure.com/) come posizione centrale per gestire le identità dei dispositivi. La pagina dispositivi Azure Active Directory consente di:

* [Configurare le impostazioni dei dispositivi](device-management-azure-portal.md#configure-device-settings)
* Per gestire i dispositivi Windows, è necessario essere un amministratore locale. [Azure ad Aggiorna questa appartenenza per i dispositivi Azure ad aggiunti](assign-local-admin.md), aggiungendo automaticamente quelli con il ruolo Gestione dispositivi come amministratori a tutti i dispositivi aggiunti.

Assicurarsi di proteggere l'ambiente [gestendo i dispositivi non aggiornati](manage-stale-devices.md)e concentrare le risorse sulla gestione dei dispositivi correnti.

* [Esaminare i log di controllo relativi al dispositivo](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Strumenti di gestione dei dispositivi supportati

Gli amministratori possono proteggere e controllare ulteriormente i dispositivi registrati e aggiunti usando strumenti di gestione dei dispositivi aggiuntivi. Questi strumenti forniscono un mezzo per applicare le configurazioni necessarie per l'organizzazione, ad esempio la crittografia della risorsa di archiviazione, la complessità delle password, le installazioni software e gli aggiornamenti software. 

Esaminare le piattaforme supportate e non supportate per i dispositivi integrati:

| Strumenti di gestione dei dispositivi| Registrazione in Azure AD| Aggiunta ad Azure AD| Aggiunta ad Azure AD ibrido|
| - | - | - | - |
| [Gestione dei dispositivi mobili (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Esempio: Microsoft Intune| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)|  |
| [Co-gestione con Microsoft Intune e Microsoft endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 e versioni successive)| | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)| ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)|  |
| [Criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Solo Windows)| | | ![Segno di spunta per questi valori.](./media/plan-device-deployment/check.png)|  |



 Si consiglia di prendere in considerazione [Microsoft Intune gestione di applicazioni mobili (MAM)](/mem/intune/apps/app-management) con o senza la gestione dei dispositivi per dispositivi iOS o Android registrati.

 Gli amministratori possono inoltre [distribuire piattaforme Virtual Desktop Infrastructure (VDI)](howto-device-identity-virtual-desktop-infrastructure.md) che ospitano sistemi operativi Windows nelle proprie organizzazioni per semplificare la gestione e ridurre i costi tramite il consolidamento e la centralizzazione delle risorse. 

### <a name="troubleshoot-device-identities"></a>Risolvere i problemi delle identità dei dispositivi

* [Risoluzione dei problemi relativi ai dispositivi tramite il comando dsregcmd](troubleshoot-device-dsregcmd.md)

* [Risoluzione dei problemi di Enterprise State Roaming in Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Se si verificano problemi durante il completamento dell'aggiunta ad Azure AD ibrido per dispositivi Windows aggiunti al dominio, vedere:

* [Risolvere i problemi dei dispositivi Windows correnti aggiunti ad Azure AD ibrido](troubleshoot-hybrid-join-windows-current.md)

* [Risolvere i problemi di join Azure AD ibrido per dispositivi Windows di livello inferiore](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare l'implementazione di Azure AD join](azureadjoin-plan.md)
* [Pianificare l'implementazione ibrida di Azure AD join](hybrid-azuread-join-plan.md)
* [Gestire le identità dei dispositivi](device-management-azure-portal.md)