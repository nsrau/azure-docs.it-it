<properties urlDisplayName="View ACS SAML" pageTitle="Visualizzare il codice SAML restituito dal Servizio di controllo di accesso (Java)" metaKeywords="" description="Informazioni su come visualizzare il codice SAML restituito dal Servizio di controllo di accesso nelle applicazioni Java ospitate in Azure." metaCanonical="" services="" documentationCenter="Java" title="How to view SAML returned by the Azure Access Control Service" authors="robmcm" videoId="" scriptId="" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Come visualizzare il codice SAML restituito dal Servizio di controllo di accesso di Azure

.In questa guida verrà descritto come visualizzare il codice SAML (Security Assertion Markup Language) sottostante restituito all'applicazione dal Servizio di controllo di accesso di Azure (ACS). Questa guida si basa sull'argomento [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse][] e fornisce codice per la visualizzazione delle informazioni relative a SAML. L'applicazione completata avrà un aspetto analogo al seguente.

![Example SAML output][saml_output]

Per altre informazioni su ACS, vedere la sezione [Passaggi successivi](#next_steps).

> [WACOM.NOTE]
> Il filtro dei Servizi di controllo di accesso di Azure (di Microsoft Open Technologies) è una Community Technology Preview. Come versione non definitiva, non è formalmente supportata da Microsoft Open Technologies, Inc. o Microsoft.

## Sommario

-   [Prerequisiti][]
-   [Aggiunta della libreria JspWriter al percorso della build e all'assembly di distribuzione][]
-   [Modifica del file JSP per la visualizzazione del codice SAML][]
-   [Esecuzione dell'applicazione][]
-   [Passaggi successivi][]


## <a name="pre"></a>Prerequisiti

Per completare le attività in questa guida, completare l'esempio disponibile in [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure utilizzando Eclipse][] e usarlo come punto di partenza per questa esercitazione.

## <a name="add_library"></a>Aggiunta della libreria JspWriter al percorso della build e all'assembly di distribuzione

Aggiungere la libreria contenente la classe **javax.servlet.jsp.JspWriter** al percorso della build e all'assembly di distribuzione. Se si usa Tomcat, la libreria sarà **jsp-api.jar**, che si trova nella cartella **lib** di Apache.

1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse su **MyACSHelloWorld**, scegliere **Percorso di compilazione**, **Configura percorso di compilazione**, fare clic sulla scheda **Librarie** e quindi fare clic su **Aggiungi JAR esterni**.
2. Nella finestra di dialogo **Selezione JAR** passare al file JAR necessario, selezionarlo e quindi fare clic su **Apri**.
3. Con la finestra di dialogo **Proprietà per MyACSHelloWorld** aperta fare clic su **Assembly di distribuzione**.
4. Nella finestra di dialogo **Assembly di distribuzione Web** fare clic su **Aggiungi**.
5. Nella finestra di dialogo **Nuova direttiva assembly** fare clic su **Voci percorso Java** e quindi fare clic su **Avanti**.
6. Selezionare la libreria appropriata e fare clic su **Fine**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà per MyACSHelloWorld**.

## <a name="modify_jsp"></a>Modifica del file JSP per la visualizzazione del codice SAML

Modificare **index.jsp** per usare il codice seguente.

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

## <a name="run_application"></a>Esecuzione dell'applicazione

1. Eseguire l'applicazione nell'emulatore di calcolo o distribuirla in Azure, usando i passaggi illustrati in [Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure utilizzando Eclipse][].
2. Avviare il browser e aprire l'applicazione Web.. Dopo avere effettuato l'accesso all'applicazione, sarà possibile visualizzare le informazioni relative al codice SAML, inclusa l'asserzione di sicurezza fornita dal provider di identità.

## <a name="next_steps"></a>Passaggi successivi

Per continuare a esplorare le funzionalità di ACS ed esercitarsi con scenari più complessi, vedere [Servizio di controllo di accesso 2.0][].

[Prerequisiti]: #pre
[Modifica del file JSP per la visualizzazione del codice SAML]: #modify_jsp
[Aggiunta della libreria JspWriter al percorso della build e all'assembly di distribuzione]: #add_library
[Esecuzione dell'applicazione]: #run_application
[Passaggi successivi]: #next_steps
[Servizio di controllo di accesso 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure usando Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
[Come autenticare gli utenti Web con il Servizio di controllo di accesso di Azure utilizzando Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse