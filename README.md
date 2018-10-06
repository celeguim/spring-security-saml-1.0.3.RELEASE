Ref: https://docs.spring.io/spring-security-saml/docs/1.0.x/reference/html/chapter-quick-start.html

4.2.2 Configuration of IDP metadata
Modify file sample/src/main/webapp/WEB-INF/securityContext.xml of the sample application and replace metadata bean as follows:

<bean id="metadata" class="org.springframework.security.saml.metadata.CachingMetadataManager">
	<constructor-arg>
		<list>
			<bean class="org.opensaml.saml2.metadata.provider.HTTPMetadataProvider">
				<constructor-arg>
					<value type="java.lang.String">http://idp.ssocircle.com/idp-meta.xml</value>
				</constructor-arg>
				<constructor-arg>
					<value type="int">5000</value>
				</constructor-arg>
				<property name="parserPool" ref="parserPool"/>
			</bean>
		</list>
	</constructor-arg>
</bean>

he settings tell system to download IDP metadata from the given URL with timeout of 5 seconds. In production system metadata should be either stored as a local file or be downloaded from a source using SSL/TLS with configured trust or which provides digitally signed metadata.

4.2.3 Generation of SP metadata
Modify file sample/src/main/webapp/WEB-INF/securityContext.xml of the sample application, replace metadataGeneratorFilter bean as follows and make sure to replace the entityId value with a string which is unique within the SSO Circle service (e.g. urn:test:yourname:yourcity):

<bean id="metadataGeneratorFilter" class="org.springframework.security.saml.metadata.MetadataGeneratorFilter">
	<constructor-arg>
		<bean class="org.springframework.security.saml.metadata.MetadataGenerator">
			<property name="entityId" value="##### replace With Unique Identifier ####"/>
			<property name="extendedMetadata">
				<bean class="org.springframework.security.saml.metadata.ExtendedMetadata">
					<property name="signMetadata" value="false"/>
					<property name="idpDiscoveryEnabled" value="true"/>
				</bean>
			</property>
		</bean>
	</constructor-arg>
</bean>

4.2.4 Compilation
# mvn package

4.2.5 Deployment
# mvn tomcat7:run

4.2.6 Uploading of SP metadata to the IDP
Download current SP metadata:

Open web browser to the URL of the deployed application.

Select Metadata information.

Select first item from category Service providers, e.g. http://localhost:8080/spring-security-saml2-sample/saml/metadata

Copy content of the Metadata textarea to your clipboard.

Upload SP metadata to the IDP:

Register yourself at www.ssocircle.com and login to the service.

Select Metadata manager and click Add new Service Provider.

Enter entityId configured in Section 4.2.3, “Generation of SP metadata” in the FQDN field.

Paste content of clipboard into the metadata information textarea.

Store metadata by pressing the Submit button.

Logout from the SSOCircle service.

4.3 Testing single sign-on and single logout
Open the front page of your SP application, select http://idp.ssocircle.com IDP and press login. The system will generate a new authentication request using SAML 2.0 protocol, digitally sign it and send it to the IDP. After authentication at IDP with your account you will be redirected back to your application and automatically signed-in.

Pressing local logout will destroy local session and logout the user. While a session is still active at the IDP an attempt to reauthenticate will proceed without need to enter credentials.

Pressing global logout will destroy both local session and the session at IDP.

You can test IDP initialized single sign-on with URL https://idp.ssocircle.com:443/sso/saml2/jsp/idpSSOInit.jsp?metaAlias=/publicidp&spEntityID=replaceWithUniqueIdentifier, after replacing the service provider identifier with the one configured as entityId in your securityContext.xml. It is possible to provide relayState data sent to your SP with parameter RelayState.


