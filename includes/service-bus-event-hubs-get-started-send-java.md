## Inviare messaggi all'hub eventi
In questa sezione si scriverà un'app di console Java per inviare eventi all'hub eventi. Si userà il provider JMS AMQP dal [progetto Apache Qpid](http://qpid.apache.org/). Il procedimento è simile a quello impiegato per l'uso di code e argomenti del bus di servizio con AMQP tramite Java e indicato [qui](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Per altre informazioni, vedere la [documentazione di Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) e la [pagina relativa al servizio di messaggistica Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. In Eclipse installare [Azure Toolkit per Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Include le librerie client Qpid JMS AMQP.

2. In Eclipse creare un nuovo progetto Java denominato **Sender**.

3. In Eclipse Package Explorer fare clic con il pulsante destro del mouse sul progetto **Sender** e scegliere **Properties**. Nel riquadro sinistro della finestra di dialogo fare clic su **Java Build Path**, quindi sulla scheda **Libraries** e infine sul pulsante **Add Library**. Selezionare **Package for Apache Qpid Client Libraries for JMS (by MS Open Tech)**, fare clic su **Next** e quindi su **Finish**.

	![][8]

4. Creare un file denominato **servicebus.properties** nella radice del progetto **Sender**, con il contenuto seguente. Ricordare di sostituire i valori di:
	- Nome dell'hub eventi.
	- Nome dello spazio dei nomi (in genere quest'ultimo è `{event hub name}-ns`).
	- Chiave **SendRule** con codifica URL (annotata al momento della creazione dell'hub eventi). È possibile creare la versione codificata con URL [qui](http://www.w3schools.com/tags/ref_urlencode.asp).

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Creare una nuova classe denominata **Sender**. Aggiungere le istruzioni `import` seguenti:

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. Aggiungere quindi il codice seguente:

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=July15_HO3-->