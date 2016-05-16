<properties
    pageTitle="Endpoint del servizio di Azure"
    description="Descrive le impostazioni dell’Endpoint del servizio di Azure nel Toolkit di Azure per Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="05/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Endpoint del servizio di Azure #

Gli endpoint del servizio di Azure determinano se l'applicazione è distribuita e gestita dalla piattaforma Azure globale, da Azure gestito da 21Vianet in Cina o da una piattaforma Azure privata. La finestra di dialogo **Endpoint del servizio** consente di specificare quali endpoint del servizio si desidera usare. Per aprire la finestra di dialogo **Endpoint del servizio**, in Eclipse, fare clic su **Finestra**, fare clic su **Preferenze**, espandere **Azure**, quindi fare clic su **Endpoint del servizio**. L'impostazione del campo **Set attivo** determina quali endpoint del servizio di Azure verranno usati per i progetti di Azure nell'area di lavoro corrente.

Di seguito viene mostrata la finestra di dialogo **Endpoint del servizio** .

![][ic719493]

## Per impostare gli endpoint del servizio ##

Nella finestra di dialogo **Endpoint del servizio**, eseguire una delle seguenti operazioni:

* Se si desidera utilizzare la piattaforma Azure globale, dall'elenco a discesa **Set attivo** selezionare **windowsazure.com** e fare clic su **OK**.
* Se si desidera utilizzare Azure gestito da 21Vianet in Cina, dall'elenco a discesa **Set attivo** selezionare **windowsazure.cn (Cina)** e fare clic su **OK**.
* Se si desidera utilizzare una piattaforma Azure privata:
    1. Fare clic su **Modifica**.
    2. Verrà visualizzata una finestra di dialogo che informa che la finestra di dialogo **Endpoint del servizio** verrà chiusa e che verrà aperto il file dei set di preferenza. Fare clic su **OK**.
    3. Nel file preferencesets.xml, creare un nuovo elemento `preferenceset`. Per questo nuovo elemento, creare gli attributi `name`, `blob`, `management`, `portalURL` e `publishsettings` e aggiungere ad essi valori che corrispondano alla piattaforma Azure privata. È possibile utilizzare i valori forniti dagli elementi esistenti `preferenceset` come modelli. **Nota**: il valore usato per l’attributo `blob` deve contenere il testo "BLOB" nell'URL.
    4. Salvare e chiudere preferencesets.xml.
    5. Riaprire la finestra di dialogo **Endpoint del servizio**.
    6. Dall'elenco a discesa **Set attivo** selezionare il set attivo creato e fare clic su **OK**.
    7. Dopo aver creato l’elemento `preferenceset` della piattaforma Azure privata, è possibile modificare i valori assegnati ad essa facendo clic sul pulsante **Modifica** nella finestra di dialogo **Endpoint dei servizi**. È inoltre possibile creare più elementi `preferenceset` della piattaforma Azure privata, se lo si desidera.

## Vedere anche ##

[Toolkit di Azure per Eclipse][]

[Installare il Toolkit di Azure per Eclipse.][]

[Creare un'applicazione Hello World per Azure in Eclipse][]

Per altre informazioni su come usare Azure con Java, visitare il [Centro per sviluppatori Java di Azure][].

<!-- URL List -->

[Centro per sviluppatori Java di Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit di Azure per Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creare un'applicazione Hello World per Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclipse.]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_0504_2016-->