---
title: Visualizzare il codice SAML restituito dal Servizio di controllo di accesso (Java)
description: Informazioni su come visualizzare il codice SAML restituito dal Servizio di controllo di accesso nelle applicazioni Java ospitate in Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 782b73f4a3d3bac5423412349a08edc5f60829da


---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Come visualizzare il codice SAML restituito dal Servizio di controllo di accesso di Azure
In questa guida verrà descritto come visualizzare il codice SAML (Security Assertion Markup Language) sottostante restituito all'applicazione dal Servizio di controllo di accesso di Azure. Questa guida si basa sull'argomento [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) e fornisce il codice per la visualizzazione delle informazioni relative a SAML. L'applicazione completata avrà un aspetto analogo al seguente.

![Esempio di output SAML][saml_output]

Per altre informazioni su ACS, vedere la sezione [Passaggi successivi](#next_steps) .

> [!NOTE]
> Il filtro dei Servizi di controllo di accesso di Azure è una Community Technology Preview. Come versione preliminare, non è formalmente supportata da Microsoft.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per completare le attività in questa guida, completare l'esempio disponibile in [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) e usarlo come punto di partenza per questa esercitazione.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Aggiunta della libreria JspWriter al percorso della build e all'assembly di distribuzione
Aggiungere la libreria contenente la classe **javax.servlet.jsp.JspWriter** al percorso della build e all'assembly di distribuzione. Se si usa Tomcat, la libreria sarà **jsp-api.jar**, che si trova nella cartella **lib** di Apache.

1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyACSHelloWorld**, scegliere **Build Path**, fare clic su **Configure Build Path**, quindi sulla scheda **Libraries** e infine su **Add External JARs**.
2. Nella finestra di dialogo **JAR Selection** passare al file JAR necessario, selezionarlo e quindi fare clic su **Open**.
3. Con la finestra di dialogo **Properties for MyACSHelloWorld** (Proprietà per MyACSHelloWorld) aperta fare clic su **Deployment Assembly** (Assembly distribuzione).
4. Nella finestra di dialogo **Web Deployment Assembly** (Assembly distribuzione Web) fare clic su **Add** (Aggiungi).
5. Nella finestra di dialogo **New Assembly Directive** fare clic su **Java Build Path Entries**, quindi scegliere **Next**.
6. Selezionare la libreria appropriata e fare clic su **Finish**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **Properties for MyACSHelloWorld** (Proprietà per MyACSHelloWorld).

## <a name="modify-the-jsp-file-to-display-saml"></a>Modifica del file JSP per la visualizzazione del codice SAML
Modificare **index.jsp** in modo che usi il codice seguente.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Eseguire l'applicazione
1. Eseguire l'applicazione nell'emulatore di calcolo o distribuirla in Azure, usando i passaggi illustrati in [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Avviare il browser e aprire l'applicazione Web. Dopo avere effettuato l'accesso all'applicazione, sarà possibile visualizzare le informazioni relative al codice SAML, inclusa l'asserzione di sicurezza fornita dal provider di identità.

## <a name="next-steps"></a>Passaggi successivi
Per continuare a esplorare le funzionalità del servizio contenitore di Azure ed esercitarsi con scenari più complessi, vedere [Servizio di controllo di accesso 2.0][Servizio di controllo di accesso 2.0].

[Prerequisiti]: #pre
[Modifica del file JSP per la visualizzazione del codice SAML]: #modify_jsp
[Aggiunta della libreria JspWriter al percorso della build e all'assembly di distribuzione]: #add_library
[Eseguire l'applicazione]: #run_application
[Passaggi successivi]: #next_steps
[Servizio di controllo di accesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png



<!--HONumber=Nov16_HO3-->


