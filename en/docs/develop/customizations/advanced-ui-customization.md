# Advanced UI Customization

The user interface of the WSO2 API-M Developer Portal, Publisher Portal and Admin Portal can be customized simply without editing the React codebase or the CSS in most cases. You will be required to modify the React codebase only if you need to do advanced customizations.

## Structure

First, let’s see how the portal’s source code is organized. The Source of the devportal, publisher and admin apps resides in the `<API_MANAGER_ROOT>/repository/deployment/server/jaggeryapps/` folder.  

 ![folder structure]({{base_path}}/assets/img/learn/ui-customize-pic0.png)

The React apps sourcecode is shipped with the distribution to make customizations easier. The basic folder structure is the same for publisher, devportal and admin apps. Let’s look at how the source code is organized.

**override**

This folder is used to organize the React component customizations. This is empty in the default pack(distribution). This is where you put your customizations to override the default React UI components.

**services**

This folder contains the utility services that are required to run the web portals. Even though the React UI portals are fully client-side rendered(CSR) apps, you still need services or proxies for some operations as given below.

- Authentication
- Reverse proxy
- Custom URLs

**site**

This contains all the static files rewuired to run the portals in the browser. It also has various static files from JS bundles, locale files, theme JSON to favicon. Inside the site, you can find the `<API_MANAGER_ROOT>/repository/deployment/server/jaggeryapps/<WEBAPP>/site/public/dist/` folder which contains the generated ReactJS application bundles and their source map files.

**source**

This folder contains the Javascript source code for both implementation and tests. None of the files here are used during runtime (get loaded into the browser). The implementation is split into two aspects, Data, and Components. Data contains pure javascript implementations of data handling (REST API innovation), utility functions, and data models (User class). The purpose of these separations is to make the data handling elements shareable and usable in non-ReactJS-based projects. And the Components folder, is used to maintain the ReactJS Component implementations. It has the whole component hierarchy of the Publisher portal.

**jaggery.conf**

This is a configuration file used for web apps in Jaggery web server, It contains the following:

- routing rules(urlMappings)
- security constraints
- welcomeFiles
- other jaggery app-related configurations.

If you are new to JaggeryJS, JaggeryJS is a javascript backend server that can render jaggery style .jag (like Pug) templates into HTML. Simply put, It’s like NodeJS + Express.

**Others**

The rest of the files are runtime configurations for eslint, jest, webpack, npm. You would probably recognize them by their names.

## Adding advanced UI customizations to WSO2 API-M UIs (Publisher and Developer Portal web-apps)

1. Navigate to the `<API-M_HOME>/repository/deployment/server/jaggeryapps` folder in a terminal and run the following command.

     ```js
     npm install
     ```

     This will install the dependencies for the `lerna` package manager.
     
2. Run the following command from the same folder in a terminal.

     ```js
     npm run bootstrap
     ```

     This will install the local package dependencies in the Publisher and Developer Portal applications.

3. Navigate to the desired web application folder (devportal, publisher or admin). Run the command given below.

    ```js
    npm run build:dev
    ```

    Note that it will continuously watch for any changes and rebuild the project.

    !!! note "Production deployment"
        The development build is not optimized and contains a large bundle size. Make sure to use the production build when the customizations are ready for production. Use the following command to get the production-ready build.
        
        ```
        npm run build:prod
        ```

### Admin Portal advanced UI customizations 

!!! note "Prerequisites"
    - **NodeJS** - This is a JavaScript runtime environment required for ReactJS development.
    - **NPM**

1. Navigate to the `<API-M_HOME>/repository/deployment/server/jaggeryapps/admin` folder in a terminal and run the following command.

     ```js
     npm ci
     ```

     This will install the local package dependencies in the Admin applications.

3. Build with the UI customizations for the Admin Portal.

     Run the following command to start the npm build. Note that it will continuously watch for any changes and rebuild the project.

    ```js
    npm run build:dev
    ```

    !!! note "Production deployment"
        The development build is not optimized and contains a large bundle size. Make sure to use the production build when the customizations are ready for production. Use the following command to get the production-ready build.
        ```
        npm run build:prod
        ```
3. Make the UI related changes in the respective folder based on the WSO2 API-M Console.

     - If you need to rewrite the admin UI completely, you can make changes in the following folder.
         - `admin/source`
     - If you want to override a specific React component or a file from the `source/src/` folder, you need to make the changes in the following folder by only copying the desired file/files.
         - `admin/override/src`

#### Overriding the API Documentation and Overview components

Any file inside `<APP-ROOT>/override/src` folder can override the original file at `<APP-ROOT>/source/src` folder. The name of the file and location relative to the source folder has to be identical. This concept applies to publisher and admin web-apps as well. For example, [1] is taking precedence over [2] when the npm build is running. 

* [1] - devportal/**override**/src/app/components/Apis/Details/Documents/Overview.jsx
* [2] - devportal/**source**/src/app/components/Apis/Details/Documents/Overview.jsx

An example for the `<APP-ROOT>/override/src` folder is shown below.

```
override
└── src
    ├── Readme.txt
    └── app
        └── components
            └── Apis
                └── Details
                    ├── Documents
                    │   └── Documentation.jsx
                    └── Overview.jsx
```

#### Adding new files to the override folder
You can add your own files to customize the UI in the `admin/override/src` folder.

For example, you can import the **NewFile.jsx** by adding the **AppOverride** prefix to the import and provide the full path relative to the override folder.

```
import NewFile from 'AppOverride/src/app/components/Apis/Details/NewFile.jsx';
```

After importing the **NewFile.jsx** the folder structure will be as follows:

```
override
└── src
    ├── Readme.txt
    └── app
        └── components
            └── Apis
                └── Details
                    ├── Documents
                    │   └── Documentation.jsx
                    └── Overview.jsx
                    └── NewFile.jsx
                    
```

A bundler error occurs if you try to import the **NewFile.jsx** from **Overview.jsx** as follows.

```
import NewFile from './NewFile.jsx';
```

### Overriding files

Any file inside `<APP-ROOT>/overrides/` folder can override the original file at `<APP-ROOT>/source/` folder. The name of the file and location relative to the source folder has to be identical. This concept applies to publisher and admin web-apps as well. For example, [1] is taking precedence over [2] when the npm build is running. 

* [1] - devportal/**override**/src/app/components/Apis/Details/Documents/Documents.jsx
* [2] - devportal/**source**/src/app/components/Apis/Details/Documents/Documents.jsx

### Adding new files to the override folder

```
override
└── src
    ├── Readme.txt
    └── app
        └── components
            └── Apis
                └── Details
                    └── Documents
                        ├── Documents.jsx
                        └── NewFile.jsx
                    
```

You can import the **NewFile.jsx** by adding the **AppOverride** prefix to the import and provide the full path relative to the override folder.

```
import NewFile from 'AppOverride/src/app/components/Apis/Details/Documents/NewFile.jsx';
```

A build error will show up if you try to import the **NewFile.jsx** from **Documents.jsx** as follows.

```
import NewFile from './NewFile.jsx';
```
## Production Build vs development build

Run the build command once and preview changes during active development. Once you run the following command at the web application root folder, it will continually watch for file changes. It also loads the React source to the browser for debugging purposes. It will make a large javascript bundle size.

```js
npm run build:dev
```

Make sure you do a production build after you finish development with the command given below. The output of the production build contains minified javascript files optimized for web browsers.

```
npm run build:prod
```


!!! note
    Production build by default check ESLint errors. ESLint is a static code analysis tool for identifying problematic patterns found in JavaScript code. We recommend always keep the customizations free from ESLint errors. But it's also possible to ignore these errors and run the production build by commenting out the following from `<WEBAPP>/webpack.config.js`

    ```javascript
    const esLintLoader = {
        enforce: 'pre',
        test: /\.(js|jsx)$/,
        loader: 'eslint-loader',
        options: {
            failOnError: true,
            quiet: true,
        },
    };
    config.module.rules.push(esLintLoader);
    ```

## Examples

Following examples will let you get familiar with the codebase.

!!! note
    React component overriding is implemented via a custom webpack loader. There are some improvements and bug fixes that went into this customer loader after the product release. If the API-M product is not a WUM updated pack, you can still apply these fixes by replacing the [loader.js from the github repo](https://github.com/wso2/carbon-apimgt/blob/master/features/apimgt/org.wso2.carbon.apimgt.publisher.feature/src/main/resources/publisher/loader.js). 

    You can simply override the file of any of the webapps (publisher, devportal, admin). We recommend you to apply this fix before trying out the following samples.

### Example 1 - Overriding API Listing Page ( devportal )

Let's see how you can override and display a custom API listing page. 

 ![api listing page]({{base_path}}/assets/img/learn/ui-customize-pic1.png)

1. Step 1 - Find the file responsible for rendering the listing view. 
    You need to find the file responsible for rendering the listing view. Using the Google Chrome browser for testing and debugging the web apps is recommended, since it provides the best toolset. 

    Install the React Developer Extension for Chrome browser from [here](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi).

    Right-click on the Devportal page and select Inspect to open the Chrome developer tools. You can also use the shortcut `Option + ⌘ + J` (on macOS), or `Shift + CTRL + J` (on Windows/Linux).
    Click the Components tab from the developer tools panel.

     ![inspect view]({{base_path}}/assets/img/learn/ui-customize-pic2.png)

    If you see a tree of `<Anonymous>` tags, it means that you are running a production build. It's hard to identify the relevant components with a production build. Make sure to run a development build running the following command.

    ```
    npm run build:dev
    ```

    !!! note
        You can find the available commands to run by examining the `package.json` file in the web app root folder.

    ![inspect view]({{base_path}}/assets/img/learn/ui-customize-pic3.png)
    ![inspect view]({{base_path}}/assets/img/learn/ui-customize-pic4.png)

    Use the inspect tool to select elements from the UI to inspect. With the development build, you can see the components responsible for displaying each section. When it comes to the API listing section you can see mainly the  "APIs", "CommonListing" and "ApiTableView" components are responsible for rendering the view. You can use the `<>` icon on the right-hand side to navigate to the source.

    You can set debug points to inspect the data used to render the list by clicking on the line numbers.

    ![inspect view]({{base_path}}/assets/img/learn/ui-customize-pic5.png)


2. Step 2 - Now let's override the APITableView.jsx.

    Open the "devportal" folder from your preferred IDE. This example uses the Visual Studio Code. 

    The complete path for the APITableView.jsx relative to the web app root is as follows.
    `devportal/source/src/app/components/Apis/Listing/ApiTableView.jsx`

    Create a copy of the same file in the following location before doing any modifications.

    `devportal/overrides/src/app/components/Apis/Listing/`

    After adding a new file you need to restart the npm build to let the changes take effect. 
    
    ```
    npm run build:dev
    ```

3. Step 3 - Modify the overridden file.

    Let's add a new `<h1>` title to the APITableView.jsx and save it.
    
    ![modified list source]({{base_path}}/assets/img/learn/ui-customize-pic6.png)

    Note that this continues running npm build triggers.

    Refresh the "devportal" listing page and view the changes you made.

    ![modified list view]({{base_path}}/assets/img/learn/ui-customize-pic7.png)

4. Step 4 - Build for production

    When you finish customizing the portal, you can run the following command to trigger a webpack production build
    ```
    npm run build:prod
    ```
    This will generate minified and optimized JS bundles in `devportal/site/public/dist` folder and these bundles will contain the customizations you have put in the override folder. If you want to apply the same customizations to other API Manager instances, simply replace this folder. No server restart is necessary.

### Example 2 - Adding a new page to API details section ( publisher )

1. Step 1 - Find the file responsible for rendering the left menu. 

    The same steps from Example 1 can be applied to locate the source files for the publisher app.

    ![publisher inspect view]({{base_path}}/assets/img/learn/ui-customize-pub-pic1.png)

    Identify that the component/file required to be overriden is the `source/src/app/components/Apis/Details/index.jsx` file.

2. Step 2 - Now let's override the index.jsx.

    Let's make a copy of this file into the following location.

    `overrides/src/app/components/Apis/Details/index.jsx`

    After adding a new file you need to restart the npm build to let the changes take effect. 
    ```
    npm run build:dev
    ```

3. Step 3 - Add the custom component.

    Add the new file (overrides/src/app/components/Apis/Details/CustomPage.jsx).

    ```
    /* eslint-disable require-jsdoc */
    import React from 'react';
    import Box from '@material-ui/core/Box';
    import Typography from '@material-ui/core/Typography';
    import Button from '@material-ui/core/Button';
    import { APIContext } from 'AppComponents/Apis/Details/components/ApiContext';

    export default function CustomPage() {
        const { api, updateAPI } = React.useContext(APIContext);
        const [displayAPI, setDisplayAPI] = React.useState({ ...api });
        const [newDescription, setNewDescription] = React.useState(displayAPI.description);
        React.useEffect(() => {
            setDisplayAPI(api);
        }, [api]);
        const updateDescription = () => {
            updateAPI({ description: newDescription });
        };
        return (
            <Box width={600}>
                <Typography variant='h3'>My Custom Page</Typography>
                <br />
                <textarea onChange={(e) => setNewDescription(e.target.value)} rows='4'>{newDescription}</textarea>
                <br />
                <Button variant='contained' color='primary' onClick={updateDescription}>
                    Update description
                </Button>
                <br />
                <br />
                <br />
                <Typography variant='caption'>{JSON.stringify(displayAPI)}</Typography>
            </Box>
        );
    }
    ```

    !!! Note
        This simple example is updating the API description. It demonstrates the usage of APIContext to view and update the API.

4. Step 4 - Link the new Component to left menu

    Now update the `overrides/src/app/components/Apis/Details/index.jsx` file as follows.

    1. Import the new page.
        ```
        import CustomPage from 'AppOverride/src/app/components/Apis/Details/CustomPage';
        ```

    2. Add a new entry to the left side menu.
        ```
        <LeftMenuItem
            text={intl.formatMessage({
                id: 'Apis.Details.index.custom.page',
                defaultMessage: 'Custom page',
            })}
            route='custompage'
            to={pathPrefix + 'custompage'}
            Icon={<ConfigurationIcon />}
        />
        ```

    3. Define the route
        ```
        <Route
            path={Details.subPaths.CUSTOMPAGE}
            component={() => <CustomPage api={api} updateAPI={this.updateAPI} />}
        />
        ```

    4. Add the "CUSTOMPAGE" constant to the "Details.subPaths" JSON

        ```
        Details.subPaths = {
        .
        .
            CUSTOMPAGE: '/apis/:api_uuid/custompage',
        .
        .
        }
        ```

5. Step 5 - Now refresh the publisher app to see the changes in action.

    ![publisher new page]({{base_path}}/assets/img/learn/ui-customize-pub-pic2.png)

4. Step 6 - Build for production

    When you finish customizing the portal, You can run the following command to trigger a webpack production build
    ```
    npm run build:prod
    ```
    This will generate minified and optimized JS bundles in `publisher/site/public/dist` folder and these bundles will contain the customizations you have put in the override folder. If you want to apply the same customizations to other API Manager instances, simply replace this folder. No server restart is necessary.

### Example 3 - Add a new input parameter to API create page ( publisher)

Let’s assume you want to add a new input parameter to API create page. Where API developers can provide a Github repository URL as a reference for API consumers. So that API consumers or application developers who are interested in this API can explore the code in the Github repository and bootstrap their work. Let’s see how the Publisher API create page UI looks like after making the customization.

![api create page]({{base_path}}/assets/img/learn/ui-customize-pub-pic3.png)


1. Step 1 - Find the file responsible for rendering the create API view. 

    Add the input field that is marked with the red rectangle.
    The same steps from the first example can be applied to locate the file or React component that is rendering the segment that you want to customize.

    ![api create page]({{base_path}}/assets/img/learn/ui-customize-pub-pic4.png)


2. Step 2 - Creating the custom React component
    Identify that the component/file required to be overriden is the `source/src/app/components/Apis/Create/Default/APICreateDefault.jsx`

    Let's make a copy of this file into the following location.
    `overrides/src/app/components/Apis/Create/Default/APICreateDefault.jsx`

    After adding a new file you need to restart the npm build to let the changes take effect. 
    ```
    npm run build:dev
    ```
    Explore the createAPI() method in APICreateDefault.jsx. It's possible to modify this and pass additional parameters from here to the RestAPI while creating the API.

3. Step 3 - Build for production

    When you finish customizing the portal, You can run the following command to trigger a webpack production build
    ```
    npm run build:prod
    ```
    This will generate minified and optimized JS bundles in `publisher/site/public/dist` folder and these bundles will contain the customizations you have put in the override folder. If you want to apply the same customizations to other API Manager instances, simply replace this folder. No server restart is necessary.
