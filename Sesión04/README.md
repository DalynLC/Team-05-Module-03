# Sesión 4 - Postwork : 

## :dart: Objetivos

- Hacer uso de la anotación `@DataProviders` como insumos de entrada de datos para los scripts.  
- Emplear fuentes de datos externas (`csv`) como insumos de entrada de datos para los scripts.



## ⚙ Requisitos

- __Software de desarrollo__
    - Java Software Development Kit (JDK)
- __Editor de código__
    - Eclipse IDE
- __Entorno de pruebas de software__
    - Selenium Java Client
    - Selenium Webdriver
    - Selenium IDE
- __Navegador__
    - Google Chrome
- __Selenium browserdriver__
    - Chromium/Chrome
- __Editor de Texto__
    - Sublime Text

## Instrucciones

Para este postwork deberás crear 3 clases distintas según el cumplimiento de los siguientes objetivos:
- Emplear fuentes de datos externas (csv) como insumos de entrada de datos para los scripts.
    - Importa la dependencia POM.xml de opencsv. 
    - Crea tu archivo csv con los datos de pruebas requeridos para cualquier funcionalidad de mercado libre. `Pro-tip:` puedes tener uno o más csv si te ayuda con la organización de los casos.
    - Crea una clase llamada DataDrivenTestingUsingCSVInSelenium donde incluiras los casos de prueba de una funcionalidad de mercadolibre tomando el csv como origen de datos. Puedes utilizar esta clase de base:

```Java
    
package com.bedu.web_automation_course;
import java.io.FileReader;
import java.io.IOException;
import org.testng.annotations.Test;
import com.opencsv.CSVReader;
import com.opencsv.exceptions.CsvValidationException;
public class DataDrivenTestingUsingCSVInSelenium {
	
	
    //Indicar la ubicación del archivo csv
    String CSV_PATH = "/Users/corinamora/BEDU/Web_Automation_Testing/Web-Automation-Testing-2022/test.csv";
    //Declaración de la variable de la clase CSVReader
    private CSVReader csvReader;
    //Declaración de una variable para leer los datos del csv
    String[] csvCell;
    
 
    @Test
    public void dataRead_CSV() throws IOException, CsvValidationException {
        //Creación del objeto de tipo CSVReader
        csvReader = new CSVReader(new FileReader(CSV_PATH));
        //uso de un loop para la lectura de todos los datos del csv 
        while ((csvCell = csvReader.readNext()) != null) {
        	
        	// se guardan en variables las posiciones del archivo csv
            String pais = csvCell[0];
            String capital = csvCell[1];
            System.out.println("El pais es : " + pais + " y su capital es :" + capital);
        }
    }
}
```
- Hacer uso de la anotación `@DataProviders` como insumos de entrada de datos para los scripts.
    - Crea una clase que contenga el método de dataprovider, con los datos de prueba, trata que sean los mismos que colocaste en el csv, de manera tal que puedas construir 2 mecanismos de origen de datos en tu proyecto. Puedes usar esta clase como base:

```Java
package com.bedu.web_automation_course;
import org.testng.annotations.DataProvider;
public class base
{
	@DataProvider (name = "nombre_dataprovider")
    public Object[][] metodoDataProvider(){
	 return new Object[][] {
		 {"Venezuela"}, 
		 {"Caracas"},
		 {"Argentina"},
		 {"Buenos Aires"},
		 {"Colombia"}, 
		 {"Bogotá"},
		 {"Ecuador"}, 
		 {"Quito"}
		 
	 };
    }	
}
```


 - Crea una clase de prueba, al igual que el ejemplo anterior, que contenga casos de prueba asociados a cualquier funcionalidad de mercado libre y que la misma haga el llamado a la clase dataprovider por medio del atributo:
 ```Java
 @Test(dataProvider = "dataprovider", dataProviderClass = data_provider.class)
 ```
 
 ## Desarrollo
 - Importación de la dependencia dentro del proyecto
 ```java
 dependencies {
	testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
	testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
	testImplementation 'org.seleniumhq.selenium:selenium-java:4.1.4'
	testImplementation 'org.testng:testng:7.4.0'
	testImplementation 'com.opencsv:opencsv:5.6'
}
 ```
 - Creación del archivo CSV que contiene los datos
 
   ![image](https://user-images.githubusercontent.com/77414220/169710874-2e79b21a-b905-47d4-8600-a4427341d8ca.png)

 ```csv
	Pedro, Perez, Pedro.Perez@gmail.com, pedro123
	Juan, Camanei, Juan.Camanei@gmail.com, juan123
 ```
 - Creación de la clase DataProvider
 ```java
 public class MyDataProvider {
    public static final String CSV_PATH = "src//cvs//createUser.csv";
    static String[] csvCell;

    @DataProvider(name = "dataProvider")
    public static Object[][] metodoDataProvider() {
        return new Object[][]{
                {"Pedro", "Perez", "Pedro.Perez@gmail.com","mypassword123"},
                {"Juan", "Camanei", "juan@camanei.com", "asd123asd"},
        };
    }

    @DataProvider(name = "dataProvider2")
    public static Object[][] metodoDataProvider1() throws IOException, CsvValidationException {
        CSVReader csvReader = new CSVReader(new FileReader(CSV_PATH));
        List<Object[]> data = new ArrayList<Object[]>();
        while ((csvCell = csvReader.readNext()) != null) {
            data.add(new String[]{csvCell[0], csvCell[1], csvCell[2], csvCell[3]});
        }
        System.out.println(data.size());
        return data.toArray(new Object[data.size()][]);
    }
}
 ```
 - Modificación del Test para cumplir con las nuevas especificaciones
 ```java
 public class sesion04_Test01 {

    WebDriver driver;

    homePage HomePage;
    createUserPage CreateUserPage;
    createUserConfirmationPage CreateUserConfirmationPage;

    @BeforeClass
    public static void setUpAll() {
        System.setProperty("webdriver.chrome.driver", "chromedriver.exe");
    }

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://www.mercadolibre.com.mx/");
        HomePage = new homePage(driver);
    }

    @AfterMethod
    public void afterEach() {
        driver.close();
    }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "dataProvider2")
    public void createAccount1(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "dataProvider")
    public void createAccount2(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }
}
 ```
