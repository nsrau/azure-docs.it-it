---
title: Gli account di lavoro aziendali e Centro per i Partner
description: Come controllare se l'azienda ha un account di lavoro impostato con Microsoft, creare un nuovo account aziendale o impostare più account di lavoro da usare con Centro per i Partner.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619556"
---
# <a name="company-work-accounts-and-partner-center"></a>Gli account di lavoro aziendali e Centro per i Partner

Centro per i partner utilizza gli account di lavoro aziendale, noto anche come tenant di Azure Active Directory (AD), per gestire account di accesso per più utenti, le autorizzazioni di controllo, i gruppi host e le applicazioni e conservare i dati di profilo. Collegando il dominio della società work email account al proprio account Centro per i Partner, dipendenti della società possono accedere al centro per i Partner per gestire le offerte di marketplace con i propri nomi utente di account di lavoro e una password.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Controllare se l'azienda ha già un account aziendale

Se l'azienda ha sottoscritto un servizio cloud Microsoft come Azure, Microsoft Intune o Office 365, già presente un dominio di account di posta elettronica di lavoro (detto anche un tenant di Azure Active Directory) può essere usato con Centro per i Partner.

Seguire questi passaggi per controllare:
1. Accedere al portale di amministrazione di Azure all'indirizzo https://portal.azure.com.
2. Selezionare **Azure Active Directory** dal menu di navigazione a sinistra e quindi selezionare **nomi di dominio personalizzati**.
3. Se hai già un account di lavoro, verrà elencato il nome di dominio.

Se l'azienda non ha ancora un account aziendale, ne verrà creato automaticamente durante il processo di registrazione del centro per i Partner.

## <a name="set-up-multiple-work-accounts"></a>Configurare più account di lavoro

Prima di decidere di usare un account di lavoro esistente, prendere in considerazione il numero di utenti nell'account di lavoro dovrà accedere Centro per i Partner. Se sono presenti utenti nell'account di lavoro che non sarà necessario accedere a Centro per i Partner, è possibile prendere in considerazione la creazione di più account di lavoro, in modo che solo gli utenti che dovranno accedere Centro per i Partner sono rappresentati in un account specifico.

## <a name="create-a-new-work-account"></a>Creare un nuovo account di lavoro

Per creare un nuovo account aziendale dell'azienda, seguire questa procedura. Potrebbe essere necessario richiedere assistenza da chiunque disponga delle autorizzazioni amministrative sull'account di Microsoft Azure dell'azienda.

1. Accedere al [portale di Microsoft Azure](https://portal.azure.com).
2. Nel menu di spostamento a sinistra, selezionare la **Azure Active Directory** -> **utenti**.
3. Selezionare **nuovo utente** e creare un nuovo account di lavoro di Azure tramite l'immissione di un indirizzo di posta elettronica e nome. Verificare che il **ruolo della Directory** è impostata su **utente** e selezionare il **Show Password** casella di controllo nella parte inferiore per visualizzare e annotare la password generata automaticamente.
4. Selezionare **Create** per salvare il nuovo utente.

L'indirizzo di posta elettronica per l'account utente deve essere un nome di dominio verificati nella directory. È possibile elencare tutti i domini verificati nella directory selezionando **Azure Active Directory** -> **nomi di dominio personalizzati** nel menu di navigazione a sinistra.

Per altre informazioni sull'aggiunta di domini personalizzati in Azure Active Directory, vedere [aggiungere o associare un dominio in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain).

## <a name="troubleshoot-work-email-sign-in"></a>Risolvere i problemi di accesso aggiuntivo tramite posta elettronica di lavoro

Se si verificano problemi ad accedere all'account di lavoro (anche noto come tenant di Azure AD), individuare che lo scenario nel diagramma seguente che meglio corrisponde alla propria situazione e seguire le procedure consigliate.

![Diagramma per la risoluzione dei problemi di lavoro accesso all'account](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'account di Marketplace commerciale nel centro per i Partner](./manage-account.md) 
