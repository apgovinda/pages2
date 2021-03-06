<<<<<<< HEAD
## Instruction to start the *Hello Application* from scratch
- Download the project zip file from [here](https://dell-edu-lab-store.s3.ap-south-1.amazonaws.com/repository/pages.zip) and extract it inside workspace folder
- Create a repository in git with the name **pages**. Keep everything default, while creating the repository, don't change anything other than default.
- Copy the *git remote add origin <repo address>* command from the landing page and execute it in the directory 
- Create a build.gradle file with following content

```groovy
plugins {
	id 'org.springframework.boot' version '2.3.1.RELEASE'
	id 'io.spring.dependency-management' version '1.0.9.RELEASE'
	id 'java'
}

group = 'com.example'
sourceCompatibility = '11'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

test {
	useJUnitPlatform()
}
```
- Create the gradle ecosystem by using the following commands
```shell script
    gradle wrapper --gradle-version 6.4.1 --distribution-type all
``` 
- Create a .gitignore file with following content
```text
HELP.md
.gradle
build/
!gradle/wrapper/gradle-wrapper.jar
!**/src/main/**
!**/src/test/**

### STS ###
.apt_generated
.classpath
.factorypath
.project
.settings
.springBeans
.sts4-cache

### IntelliJ IDEA ###
.idea
*.iws
*.iml
*.ipr
out/

### NetBeans ###
/nbproject/private/
/nbbuild/
/dist/
/nbdist/
/.nb-gradle/

### VS Code ###
.vscode/
```
- Open the project in Intellij Idea, select the import gradle project option in bottom right corner and  set project SDK to JDK 11
- Create two folders **src/main/java** and **src/test/java** under project root directory. Mark them as sources root and test root respectively.
- Create two packages **org.dell.kube.pages** and **org.dell.kube.pagesapi** under *src/test/java*
- Create a Test class called **PagesApplicationTests.java** under package **org.dell.kube.pages** with below content
```java
package org.dell.kube.pages;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class PagesApplicationTests {

	@Test
	void contextLoads() {
	}

}
```
- Create a Test class called **HomeControllerTests.java** under package **org.dell.kube.pages** with below content
```java
package org.dell.kube.pages;

import org.junit.jupiter.api.Test;

import static org.assertj.core.api.Assertions.assertThat;

public class HomeControllerTests {
    private final String message = "YellowPages";

    @Test
    public void itSaysYellowPagesHello() throws Exception {
        HomeController controller = new HomeController();

        assertThat(controller.getPage()).contains(message);
    }


}
```
- Create a Test class called **HomeApiTest** under the package **org.dell.kube.pagesapi** with below content
```java
package org.dell.kube.pagesapi;

import org.dell.kube.pages.PageApplication;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;

import static org.assertj.core.api.Assertions.assertThat;
import static org.springframework.boot.test.context.SpringBootTest.WebEnvironment.RANDOM_PORT;

@SpringBootTest(classes = PageApplication.class, webEnvironment = RANDOM_PORT)
public class HomeApiTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    @Test
    public void readTest() {
        String body = this.restTemplate.getForObject("/", String.class);
        assertThat(body).contains("YellowPages");
    }

    @Test
    public void healthTest(){
        String body = this.restTemplate.getForObject("/actuator/health", String.class);
        assertThat(body).contains("UP");
    }
}
```
- Create a *settings.gradle* file with below content in the root project
```groovy
rootProject.name = 'pages'
```
- Push the content to the local git repository
```shell
git add .
git commit -m "MESSAGE"
```
=======
## Instructions to Reach Inmemory Solution
As part of the *inmemory-start* checkout 2 Test classes would be added to the codebase. On build the application would fail as there is no code to support the test cases. Follow the below instructions to reach solution so that the test cases will pass.
- Create new File Pages.java in src/main under *org.dell.kube.pages* package
```java
package org.dell.kube.pages;

public class Page {

    private Long id;
    private String businessName;
    private Long categoryId;
    private String address;
    private String contactNumber;

    public Page(){}

    public Page(String businessName, String address, long categoryId, String contactNumber) {
        this.businessName = businessName;
        this.address = address;
        this.categoryId = categoryId;
        this.contactNumber = contactNumber;
    }

    public Page(long id, String businessName, String address, long categoryId, String contactNumber) {
        this.id = id;
        this.businessName = businessName;
        this.address = address;
        this.categoryId = categoryId;
        this.contactNumber = contactNumber;
    }
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getBusinessName() {
        return businessName;
    }

    public void setBusinessName(String businessName) {
        this.businessName = businessName;
    }

    public Long getCategoryId() {
        return categoryId;
    }

    public void setCategoryId(Long categoryId) {
        this.categoryId = categoryId;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getContactNumber() {
        return contactNumber;
    }

    public void setContactNumber(String contactNumber) {
        this.contactNumber = contactNumber;
    }
}
```
- Create new IPageRepository.java in src/main under *org.dell.kube.pages* package
```java
package org.dell.kube.pages;

import java.util.List;

public interface IPageRepository {
    public Page create(Page page);
    public Page read(long id);
    public List<Page> list();
    public Page update(Page page, long id);
    public void delete(long id);
}
```
- Create new InMemoryPageRepository which implements IPageRepository
```java
package org.dell.kube.pages;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class InMemoryPageRepository implements IPageRepository{
    Map<Long,Page> repo = new HashMap<Long,Page>();
    long counter;

    @Override
    public Page create(Page page) {
        page.setId(++counter);
        repo.put(page.getId(),page);
        return repo.get(page.getId());
    }

    @Override
    public Page read(long id) {
        return repo.get(id);
    }

    @Override
    public List<Page> list() {
        return new ArrayList<Page>(repo.values());
    }

    @Override
    public Page update(Page page, long id) {
        Page data = repo.get(id);
        if(data != null){
            page.setId(id);
            repo.put(page.getId(),page);
            data = page;
        }
        return data;
    }

    @Override
    public void delete(long id) {
       repo.remove(id);
    }
}
```
- Create a bean called  **pageRepository** in PageApplication.java which returns an implementation of *IPageRepository*
- Create a PageController.java in src folder. Create an Instance of IPageRepository and intialiase it with a constructor injection
```java
package org.dell.kube.pages;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/pages")
public class PageController {

    private IPageRepository pageRepository;
    public PageController(IPageRepository pageRepository)
    {
        this.pageRepository = pageRepository;
    }
    @PostMapping
    public ResponseEntity<Page> create(@RequestBody Page page) {
        Page newPage= pageRepository.create(page);
        return new ResponseEntity<Page>(newPage, HttpStatus.CREATED);
    }
    @GetMapping("{id}")
    public ResponseEntity<Page> read(@PathVariable long id) {
        Page page = pageRepository.read(id);
        if(page!=null)
            return new ResponseEntity<Page>(page,HttpStatus.OK);
        else
            return new ResponseEntity(HttpStatus.NOT_FOUND);
    }
    @GetMapping
    public ResponseEntity<List<Page>> list() {
        List<Page> pages= pageRepository.list();
        return new ResponseEntity<List<Page>>(pages,HttpStatus.OK);
    }
    @PutMapping("{id}")
    public ResponseEntity<Page> update(@RequestBody Page page, @PathVariable long id) {
        Page updatedPage= pageRepository.update(page,id);
        if(updatedPage!=null)
            return new ResponseEntity<Page>(updatedPage,HttpStatus.OK);
        else
            return new ResponseEntity(HttpStatus.NOT_FOUND);
    }
    @DeleteMapping("{id}")
    public ResponseEntity delete(@PathVariable long id) {
        pageRepository.delete(id);
        return new ResponseEntity(HttpStatus.NO_CONTENT);
    }
}
```
- Run the application and test by making CRUD operations using any CRUD tool like ARC, POSTMAN or CURL.
- Build and Publish the docker image tag as **repo** and change the tag value both in pages-deployment.yaml and pipeline.yaml also
- Check in the code to start github actions to deploy in PKS Cluster
- Verify the deployments in PKS Cluster and test the application by opening it in browser as per instructions given in previous labs
>>>>>>> inmemwork
