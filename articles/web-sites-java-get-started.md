<properties linkid="develop-java-tutorials-web-site-get-started" urlDisplayName="Get started with Azure" pageTitle="Get started with Microsoft Azure Web Sites using Java" metaKeywords="" description="This tutorial shows you how to deploy a Java web site to Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="Java" title="Get started with Azure and Java" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

Introduzione all'uso di siti Web di Azure con Java
==================================================

In questa esercitazione verrà illustrato come creare un sito Web in Microsoft Azure utilizzando Java e la raccolta di applicazioni di Azure o l'interfaccia utente della configurazione dei siti Web di Azure.

Se non si desidera utilizzare nessuna di queste tecniche, ad esempio perché si desidera personalizzare il proprio contenitore di applicazioni, vedere [Caricamento di un sito Web Java personalizzato in Azure](../web-sites-java-custom-upload).

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oppure [iscriversi per ottenere una versione di valutazione gratuita](/en-us/pricing/free-trial/?WT.mc_id=A261C142F).

Creazione di un sito Web Java utilizzando la raccolta di applicazioni di Azure
==============================================================================

Queste informazioni mostrano come utilizzare la raccolta di applicazioni di Azure per selezionare un contenitore di applicazioni Java (Apache Tomcat o Jetty) per il proprio sito Web.

Di seguito viene illustrato l'aspetto di un sito Web creato utilizzando Tomcat dalla raccolta di applicazioni:

![Sito Web che utilizza Apache Tomcat](./media/web-sites-java-get-started/tomcat.png)

Di seguito viene illustrato l'aspetto di un sito Web creato utilizzando Jetty dalla raccolta di applicazioni:

![Sito Web che utilizza Jetty](./media/web-sites-java-get-started/jetty.png)

1.  Accedere al portale di gestione di Azure.
2.  Fare clic su **New**, **Compute**, **Web Site** e infine su **From Gallery**.
3.  Selezionare uno dei server applicazioni Java dall'elenco di app, ad esempio **Apache Tomcat** o **Jetty**.
4.  Fare clic su **Next**.
5.  Specificare il nome di URL.
6.  Scegliere un'area, ad esempio **West US**.
7.  Fare clic su **Complete**.

Il sito Web verrà creato dopo alcuni secondi. Per visualizzare il sito Web, nella visualizzazione **Web Sites** del portale di gestione di Azure, attendere che lo stato visualizzato corrisponda a **Running**, quindi fare clic sull'URL relativo al sito Web.

Ora che si è creato un sito Web con un contenitore per le app, vedere la sezione **Passaggi successivi** per informazioni sul caricamento dell'applicazione nel sito Web.

Creazione di un sito Web Java utilizzando l'interfaccia utente di configurazione di Azure
=========================================================================================

Queste informazioni mostrano come utilizzare l'interfaccia utente di configurazione di Azure per selezionare un contenitore di applicazioni Java (Apache Tomcat o Jetty) per il proprio sito Web.

1.  Accedere al portale di gestione di Azure.
2.  Fare clic su **New**, **Compute**, **Web Site** e infine su **Quick Create**.
3.  Specificare il nome di URL.
4.  Scegliere un'area, ad esempio **West US**.
5.  Fare clic su **Complete**. Il sito Web verrà creato dopo alcuni secondi. Per visualizzare il sito Web, nella visualizzazione **Web Sites** del portale di gestione di Azure, attendere che lo stato visualizzato corrisponda a **Running**, quindi fare clic sull'URL relativo al sito Web.
6.  Sempre dall'interno del portale di gestione di Azure, nella visualizzazione **Web Sites**, fare clic sul nome del sito Web per aprirne il dashboard.
7.  Fare clic su **Configure**.
8.  Nella sezione **General** abilitare **Java** facendo clic sulla versione disponibile.
9.  Vengono visualizzate le opzioni per il contenitore Web, ad esempio Tomcat e Jetty. Selezionare il contenitore Web che si desidera utilizzare.
10. Fare clic su **Save**.

Dopo alcuni secondi il sito Web diventerà basato su Java. Per confermare che il sito Web sia basato su Java, nella visualizzazione **Web Sites** del portale di gestione di Azure, attendere che lo stato visualizzato corrisponda a **Running**, quindi fare clic sull'URL relativo al sito Web. Si noti che nella pagina verrà visualizzato del testo che indica che il nuovo sito è un sito Web basato su Java.

Ora che si è creato un sito Web con un contenitore per le app, vedere la sezione **Passaggi successivi** per informazioni sul caricamento dell'applicazione nel sito Web.

Passaggi successivi
===================

A questo punto si disporrà di un server applicazioni Java in esecuzione come sito Web Java in Azure. Per aggiungere la propria applicazione o pagina Web, vedere [Aggiunta di un'applicazione o di una pagina Web a un sito Web Java](../web-sites-java-add-app).

