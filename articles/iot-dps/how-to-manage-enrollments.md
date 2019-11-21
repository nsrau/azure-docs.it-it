---
title: Manage device enrollments for Azure IoT Hub Device Provisioning Service in the Azure portal
description: Come gestire le registrazioni dei dispositivi per il servizio Device Provisioning nel portale di Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 03559183e46182c4f80fe3d16ec542add9b1692b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229723"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Come gestire le registrazioni dei dispositivi con il portale di Azure

La *registrazione dei dispositivi* crea un record di un singolo dispositivo o di un gruppo di dispositivi registrato in un dato punto con il Servizio Device Provisioning in hub IoT di Azure. Il record di registrazione contiene la configurazione iniziale desiderata per i dispositivi come parte della registrazione, incluso l'hub IoT desiderato. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning.


## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi

Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo di registrazioni** è una voce relativa a un gruppo di dispositivi che condividono un meccanismo di attestazione comune dei certificati X.509, firmato dallo stesso certificato di firma, che può essere il [certificato radice](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o il [certificato intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), usato per generare il certificato del dispositivo sul dispositivo fisico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Si noti che è possibile registrare solo i dispositivi che usano il meccanismo di attestazione X.509 come *gruppi di registrazione*. 

    È possibile creare un gruppo di registrazioni per un gruppo di dispositivi nel portale usando la procedura seguente:

  1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.  
  1. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.  
  1. Nel servizio di provisioning:  
     a. Fare clic su **Gestisci registrazioni**, quindi selezionare la scheda **Gruppi di registrazione**.  
     b. Fare clic sul pulsante **Add** (Aggiungi) in alto.  
     c. Quando viene visualizzato il pannello "Add Enrollment Group" (Aggiungi gruppo di registrazione), inserire le informazioni necessarie per la voce dell'elenco di registrazione.  Il **nome del gruppo** è obbligatorio. Selezionare inoltre "CA o Intermedio" per **Tipo di certificato** e caricare il **certificato primario** radice per il gruppo di dispositivi.  
     d. Fare clic su **Salva** Dopo avere creato il gruppo di registrazione, sotto la scheda **Enrollment Groups** (Gruppi di registrazione) verrà visualizzato il nome del gruppo.  

     [![Enrollment group in the portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Una **registrazione individuale** è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati x.509 o token di firma di accesso condiviso (in un TPM fisico o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

    È possibile creare una registrazione singola nel portale mediante la procedura seguente:

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    1. Selezionare dall'elenco di risorse il servizio di provisioning dei dispositivi per cui si desidera registrare il dispositivo.
    1. Nel servizio di provisioning:  
       a. Fare clic su **Gestisci registrazioni**, quindi selezionare la scheda **Registrazioni singole**.  
       b. Fare clic sul pulsante **Add** (Aggiungi) in alto.   
       c. Quando viene visualizzato il pannello "Add Enrollment" (Aggiungi registrazione), inserire le informazioni necessarie per la voce dell'elenco di registrazione. Selezionare il **meccanismo** di attestazione per il dispositivo (X.509 o TPM). Per l'attestazione X.509 è necessario caricare il **certificato primario** foglia per il dispositivo. Per TPM è necessario immettere la **chiave dell'attestazione** e l'**ID di registrazione** per il dispositivo.  
       d. Fare clic su **Salva** Dopo avere creato il gruppo di registrazione, sotto la scheda **Individual Enrollments** (Registrazioni singole) verrà visualizzato il nome del gruppo.  

       [![Registrazione singola sul portale](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione
È possibile aggiornare una voce di registrazione esistente sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
1. Passare alla voce di registrazione che si desidera modificare. Selezionando la voce, si aprirà un riepilogo dei dati per la registrazione del dispositivo. 
1. In questa pagina è possibile modificare gli elementi diversi dal tipo protezione e dalle credenziali, ad esempio l'hub IoT a cui il dispositivo dovrà essere collegato e l'ID del dispositivo. È anche possibile modificare lo stato iniziale del dispositivo gemello. 
1. Al termine, fare clic su **Salva** per aggiornare la registrazione del dispositivo. 

    ![Aggiornare una registrazione sul portale](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Rimuovere una registrazione del dispositivo
Nei casi in cui non è necessario eseguire il provisioning dei dispositivi in alcun hub IoT, è possibile rimuovere la voce di registrazione correlata sul portale usando la procedura seguente:

1. Aprire il servizio di provisioning dei dispositivi nel portale di Azure e fare clic su **Manage enrollments** (Gestisci registrazioni). 
1. Individuare e selezionare la voce di registrazione che si desidera modificare. 
1. Fare clic su **Elimina** nella parte superiore, quindi selezionare **Sì** quando viene richiesto di confermare. 
1. Una volta completata l'azione, la voce apparirà rimossa dall'elenco delle registrazioni dei dispositivi. 
 
    ![Rimuovere una registrazione dal portale](./media/how-to-manage-enrollments/remove-enrollment.png)


