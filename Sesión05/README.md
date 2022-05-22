# Sesión 5 # Postwork: 

## :dart: Objetivos

- Crear una base de datos y tablas en Mysql.
- Desarrollar la conexión a la base de prueba en los scripts de pruebas automatizados.
- Adaptar los scripts de pruebas para que consuman los datos provenientes de la ejecución de Querys en la Base de datos.


## ⚙ Requisitos

- Software de desarrollo
    - Java Software Development Kit (JDK)
- Editor de código
    - Eclipse IDE
- Entorno de pruebas de software
    - Selenium Java Client
    - Selenium Webdriver
    - Selenium IDE
- Navegador
    - Google Chrome
- Selenium browserdriver
    - Chromium/Chrome
- Base de Datos
    - MySql Server
    - MySql Workbench


## Instrucciones

- Crea una base de datos MySql a través de un Query en MySQL Workbench.
- Crea una o varias tablas según la/s funcionalidades de mercado libre escogida para el desarrollo de tu proyecto.
- Ingresa los registros a las tablas según los datos que requieras para las pruebas de tu funcionalidad. `Pro-Tip`: puedes agregar una tabla `general` que contenga información para tus pruebas y que no esté relacionada con alguna funcionalidad en particular.
- Realiza la conexión a la base de datos. `Buena Práctica`: puedes realizar la conexión en una clase independiente e importarla en tu clase de prueba.
- Realiza uno o varios scripts de pruebas según la necesidad de tu funcionalidad.
- Realiza aserciones con los datos obtenidos de la consulta ejecutada.
- Utiliza las consultas a la base de datos como insumo para tus pruebas, es decir, guarda información en las tablas que puedas luego consultar y usarla por ejemplo para llenar campos en formularios.

## Desarrollo
- Creación de la base de datos e inserción de los datos.

![01](https://user-images.githubusercontent.com/77414220/169711861-18343e75-83ef-44c3-883a-5ff8714f2b87.PNG)

![02](https://user-images.githubusercontent.com/77414220/169711873-ee724f93-1f16-4f30-81b9-07cca7c62919.PNG)

![03](https://user-images.githubusercontent.com/77414220/169711875-38e74055-edde-41a4-afd1-95b85fbd5216.PNG)


- Creación de la clase para la conexión.
```java
public class databaseConnection {

    Connection con;
    ResultSet res;

    public void Conector() {

        try {

            String DB_URL = "jdbc:mysql://localhost:3306/crearusuario";
            String DB_USER = "root";
            String DB_PASSWORD = "root";
            con = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
            String dbClass = "com.mysql.cj.jdbc.Driver";
            try {
                Class.forName(dbClass);

            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }

            if (con != null) {
                System.out.println("Conectado a MySQL");
            }

        } catch (SQLException ex) {
            System.out.println("ERROR: La URL de la conexión no es válida o el usuario y clave de la BD");
            ex.printStackTrace();
        }

    }

    public ResultSet executeQuery(String query) throws SQLException {
        Statement stmt = con.createStatement();
        res = stmt.executeQuery(query);
        return res;

    }

    public void closeConnexion() throws SQLException {
        if (res != null) {
            res.close();
            System.out.println("ResultSet cerrado exitosamente...");
        }

        if (con != null) {
            con.close();
            System.out.println("Conexión cerrada exitosamente...");
        }
    }

    public int getColumnNumber(ResultSet res) throws SQLException {
        ResultSetMetaData rsmd = res.getMetaData();
        return rsmd.getColumnCount();

    }
    public int getRowsNumber(ResultSet res) throws SQLException {
        int filas = 0;
        while (res.next()){
            filas++;
        }
        return filas;
    }

    public void printResult(ResultSet res) throws SQLException {

        while (res.next()) {

            for (int i = 1; i <= getColumnNumber(res); i++) {
                System.out.print(" | " + res.getString(i));
                if (i == getColumnNumber(res)) {
                    System.out.println(" | " + res.getString(i));
                }
            }
        }

    }
}
```
- Importación de la instrucción para conexión de bases de datos.
```java
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
    testImplementation 'org.seleniumhq.selenium:selenium-java:4.1.4'
    testImplementation 'org.testng:testng:7.4.0'
    testImplementation 'com.opencsv:opencsv:5.6'
    testImplementation 'mysql:mysql-connector-java:8.0.29'
}
```
- Creación del data providere para obtener los datos de la base de datos.
```java
public class MyDataProvider {

    @DataProvider(name = "MySQL_dataprovider")
    public Object[][] mySQL_Data() {

        int rowCount = 0;
        int columnCount = 0;
        String myData[][] = null;
        databaseConnection conect = new databaseConnection();
        ResultSet res;

        try {
            conect.Conector();

            // Ejecutar consulta (query) a la BD
            String query = "SELECT name, lastName, email, password FROM User";
            res = conect.executeQuery(query);

            // Obtener nro de filas y columnas
            columnCount = conect.getColumnNumber(res);
            rowCount = conect.getRowsNumber(res);

            System.out.println("Columnas : " + columnCount);
            System.out.println("Filas : " + rowCount);

            // Inicializar la matriz
            myData = new String[rowCount][columnCount];
            res = conect.executeQuery(query);

            // Llenar la matriz con el resultado de la consulta MySQL
            for(int row=0; row<rowCount; row++)
            {
                res.next();

                for(int col=1; col<=columnCount; col++)
                    myData[row][col-1] = res.getString(col);
            }

            //Cerrar la conexión
            conect.closeConnexion();

        }

        catch (Exception e) {
            e.printStackTrace();
        }

        return myData;

    }
}
```
- Modificación de las pruebas para adaptarlas a los nuevos requerimientos.
```java
public class sesion05_Test01 {

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

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "MySQL_dataprovider")
    public void createAccount(String name, String lastName, String email, String password) throws InterruptedException {
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

