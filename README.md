# Request Management Tool (RMT)
Last updated: 2024-03-27
Author: Muna Adan

## Overview
The RMT is a multi-page PHP web application backed by MySQL that handles the administration of accessibility requests from clients. A request comes in via the `new-request` page and can be assigned a status, client, and attached to an employee to triage and/or fulfill the client request.

The RMT is built on top of the WET framework (Web Experience Toolkit) by the treasury board of Canada.

The life cycle of a request is as follows:

**Under construction**

1. A user makes as request via the RMT. They select a category (called a catalogue in the code), a service, and potential one of two sub services, depending on each category.
2. An administrator is then responsible for initial triage and assigning an employee to the request. The types of requests are:
```
Client Needs Assessment
Adaptive Technology Support
Loan B ank Services
A11y (accessibility) services
ACP
ICT audits
Doc audits
Advice and recommendations
Procurement
EPMO
```
3.

### Technologies
1. PHP
2. JS
3. MySQL
4. WET4 (Web experience toolkit)

## Data Model

The application's data model consists of the following tables:

1.  **tblaccounttype**: Stores account type information for logged-in users, including the account type description in English and French.
2.  **tbladminlog**: Logs administrative activities or actions performed by administrators or privileged users. Each log entry includes the admin's ID, the request being triaged, and its corresponding status.
3.  **tblcatalogue**: Contains a catalog or directory of services offered by the RMT (Request Management Tool). The data from this table populates the first dropdown on the "New Request" page.
4.  **tblcommlog**: Logs non-administrative communication-related activities within the application, such as messages, notifications, or emails sent.
5.  **tblcontacts**: Stores contact information for individuals or organizations related to the application, including team names and escalation contacts.
6.  **tblcss**: Stores feedback collected from the RMT tool.
7.  **tblservices**: Contains details about various services offered or managed by the application. In some cases, the data from this table populates the second dropdown on the "New Request" page.
8.  **tblsources**: Relates to sub-services used within the application. In some cases, the data from this table populates the third dropdown on the "New Request" page.
9.  **tblstatus**: Stores a list of possible states or statuses that an RMT request can have.
10.  **tblsubservices**: Contains information about sub-services of the main services offered by the application. The data from this table populates the second dropdown on the "New Request" page.
11.  **tbltriage**: Used for triaging or prioritizing tasks, issues, or requests within the application.
12.  **tblusers**: Stores user account information and authentication data for the application's users.

This documentation provides an overview of the purpose and usage of each table within the application's data model. It serves as a reference for understanding the relationships between different data entities and their roles in supporting the application's functionality.

## AJAX actions

The RMT takes actions via AJAX to fill in extra information required to search or add a new request.

For example, this is the flow of selecting a sub-service ID for a given catalogue with added comments.

*note*: Comments like the ones below would help developers onboard onto the project faster.

```php
<?php
/**
 * Sub-Service Dropdown Generator
 *
 * This script is responsible for generating an HTML dropdown menu populated
 * with active sub-services based on a provided service ID. It is used as part of
 * the "add request" feature or form in the application.
 *
 * Dependencies:
 * - sql.php (included for database connection and utility functions)
 *
 * Flow:
 * 1. Retrieve the service ID from the GET request parameter 'v1'
 * 2. Construct an SQL query to fetch active sub-services for the given service ID
 * 3. Execute the query and generate the HTML dropdown menu
 * 4. Populate the dropdown options with sub-service names from the query results
 * 5. Render the results on the page
 */

require('sql.php');

// Grab the catalogue id
if (!empty($_GET['v1'])) {
    $serviceid = mysqli_real_escape_string($link, $_GET['v1']);
} else {
    $serviceid = "";
}

// Check if results, otherwise return empty result
$sql = "SELECT * FROM tblsubservices WHERE serviceid='$serviceid' AND status='1' ORDER BY nameen ASC";
$result = mysqli_query($link, $sql);

// List it
if (mysqli_num_rows($result) > 0) {
?>
    <label for="subserviceid"><span class="field-name">Sub-service name:</span></label>
    <select class="form-control" id="subserviceid" name="subserviceid">
        <option value="">Select a sub-service name</option>
        <?php
        $sql2 = "SELECT * FROM tblsubservices WHERE serviceid='$serviceid' AND status='1' ORDER BY nameen ASC";
        $result2 = mysqli_query($link, $sql2);
        while ($row = mysqli_fetch_array($result2)) {
        ?>
            <option value="<?php echo $row['id']; ?>"><?php echo $row['nameen']; ?></option>
        <?php
        }
        ?>
    </select>
<?php
}

// Close connection
mysqli_close($link);
?>
```

## Recommendations

To improve the maintainability, scalability, and overall health of the application, the following recommendations are proposed:

### 1. Initiate a Refactoring Process

It is recommended to initiate a refactoring process to address any outstanding technical debt and reduce the administrative burden on staff. This refactoring should focus on:

-   Untangling the tightly coupled and hardcoded application logic, making it more modular and easier to modify.
-   Transitioning from hardcoded logic to a more flexible and extensible architecture that utilizes POST requests to pass necessary information, thereby enhancing the code's editability.

The refactoring effort is expected to increase bug resolution and feature development velocity by improving the codebase's overall quality and maintainability.

### 2. Implement a Staged Deployment Process

To mitigate the risk of introducing bugs or downtime during the refactoring process, it is recommended to implement a staged deployment process with the following steps:

1.  **Pilot Phase**: Conduct a pilot or proof-of-concept implementation on a separate deployment environment to validate the refactoring approach and identify potential issues before applying changes to the production environment.
2.  **Tiered Environment System**: Establish a tiered environment system comprising separate instances for development, testing, staging, and production. This will ensure that new development and bug fixes can be thoroughly tested and validated before being deployed to the production environment, minimizing the risk of negative impact on uptime.

### 3. Evaluate Modern Web Frameworks

To enhance developer productivity and leverage industry-standard best practices, it is recommended to evaluate modern web frameworks like Laravel for potential adoption. Laravel offers a robust set of features and tools that can streamline the development process, improve code organization, and facilitate easier maintenance and scalability.

### 4. Implement Better Git Practices

To maintain a clean and organized codebase, it is essential to implement more streamlined git practices.

This includes:

-   Adopting a consistent branching strategy (e.g., Git Flow or Trunk-Based Development) to manage code changes and releases.
-   Enforcing code reviews and merging policies to ensure code quality and prevent regressions.
-   Adhering to commit message conventions for better traceability and understanding of code changes.
-   Leveraging continuous integration and automated testing to catch issues early in the development cycle.
