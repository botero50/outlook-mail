# outlook-mail
This is a demo of how to get e-mails from the API using Imap Pop3

First of all you need to create a Maven project and then add a java file with this code

## Code

import java.util.Properties;
import javax.mail.Address;
import javax.mail.BodyPart;
import javax.mail.Folder;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.Multipart;
import javax.mail.Part;
import javax.mail.Session;
import javax.mail.Store;
import javax.mail.internet.InternetAddress;
import javax.mail.search.FromTerm;
import javax.mail.search.SearchTerm;
import javax.mail.search.SubjectTerm;

public class GetMails {
        public static void main(String[] args) {
            Properties props = System.getProperties();
            props.setProperty("mail.store.protocol", "imaps");
            props.put("mail.imap-mail.outlook.com.ssl.enable", "true");
            props.put("mail.pop3.host", "outlook.com");
            props.put("mail.pop3.port", "995");
            props.put("mail.pop3.starttls.enable", "true");
            try {
                Session session = Session.getInstance(props, null);
                Store store = session.getStore();
                store.connect("imap-mail.outlook.com", "YourUser@hotmail.com", "Your Password");
                session.setDebug(true);
                Folder inbox = store.getFolder("INBOX");
                inbox.open(Folder.READ_ONLY);

                //here you need to change the address if you wanth to search mails from an specific one
                SearchTerm sender = new FromTerm(new InternetAddress("noreply@steampowered.com"));

                Message[] messages = inbox.search(sender);
                System.out.println(messages);


                System.out.println(messages[1].getSubject());

                System.out.println(getMessageContent(messages[1]));

                store.close();

            } catch (Exception mex) {
                mex.printStackTrace();
            }
        }
        
        public static String getMessageContent(Message message) throws MessagingException {
            try {
                Object content = message.getContent();
                if (content instanceof Multipart) {
                    StringBuffer messageContent = new StringBuffer();
                    Multipart multipart = (Multipart) content;
                    for (int i = 0; i < multipart.getCount(); i++) {
                        Part part = multipart.getBodyPart(i);
                        if (part.isMimeType("text/plain")) {
                            messageContent.append(part.getContent().toString());
                        }
                    }
                    return messageContent.toString();
                }
                return content.toString();

            } catch (Exception e) {
                e.printStackTrace();
            }
            return "";
        }
        
        public boolean match(Message message) {
            try {
                if (message.getSubject().contains("Ordretest")) {
                    System.out.println("match found");
                    return true;
                }
            } catch (MessagingException ex) {
                ex.printStackTrace();
            }
            return false;
        };
    }
    
 ## POM - Add this to the pom
 
  <dependency>
		    <groupId>com.sun.mail</groupId>
		    <artifactId>pop3</artifactId>
		    <version>2.0.0-RC4</version>
		</dependency>
		<dependency>
		    <groupId>com.sun.mail</groupId>
		    <artifactId>smtp</artifactId>
		    <version>2.0.0-RC4</version>
		</dependency>
		   <dependency>
        <groupId>javax.mail</groupId>
	        <artifactId>javax.mail-api</artifactId>
	        <version>1.5.3</version>
	    </dependency>
	    <dependency>
	        <groupId>com.sun.mail</groupId>
	        <artifactId>javax.mail</artifactId>
	        <version>1.5.3</version>
	    </dependency>
		<dependency>
		    <groupId>com.sun.mail</groupId>
		    <artifactId>imap</artifactId>
		    <version>2.0.0-RC4</version>
		</dependency>
		<dependency>
		    <groupId>com.google.api-client</groupId>
		    <artifactId>google-api-client</artifactId>
		    <version>1.30.9</version>
		</dependency>
		<dependency>
		    <groupId>com.google.apis</groupId>
		    <artifactId>google-api-services-gmail</artifactId>
		    <version>v1-rev110-1.25.0</version>
		</dependency>
    <dependency>
        <groupId>javax.mail</groupId>
        <artifactId>javax.mail-api</artifactId>
        <version>1.5.3</version>
        <exclusions>
            <exclusion>
                <artifactId>activation</artifactId>
                <groupId>javax.activation</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>com.sun.mail</groupId>
        <artifactId>javax.mail</artifactId>
        <version>1.5.3</version>
        <exclusions>
            <exclusion>
                <artifactId>activation</artifactId>
                <groupId>javax.activation</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
        
    </build>
    
 ## Required Library - Download the JavaMail library and import it in the project using java building path / add external jars
 https://www.oracle.com/technetwork/java/index-138643.html
