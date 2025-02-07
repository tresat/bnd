# bnd-resolver-maven-plugin

The `bnd-resolver-maven-plugin` is a bnd based plugin to resolve bundles from bndrun files.

## What does the `bnd-resolver-maven-plugin` do?

Point the plugin to one or more bndrun files in the same project. It will resolve the -runbundles value.

```
    <plugin>
        <groupId>biz.aQute.bnd</groupId>
        <artifactId>bnd-resolver-maven-plugin</artifactId>
        <version>${bnd.version}</version>
        <configuration>
            <failOnChanges>false</failOnChanges>
            <bndruns>
                <bndrun>mylaunch.bndrun</bndrun>
            </bndruns>
        </configuration>
        <executions>
            <execution>
                <goals>
                    <goal>resolve</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
```

Here's an example setting the `bundles` used for resolution.

```
    ...
    <configuration>
        ...
        <bundles>
            <bundle>bundles/org.apache.felix.eventadmin-1.4.8.jar</bundle>
            <bundle>bundles/org.apache.felix.framework-5.4.0.jar</bundle>
        </bundles>
    </configuration>
    ...
```

## Executing the resolve operation

Since the resolve operation is not associated with any maven build phase, it must in invoked manually.

Here's an example invocation:
```
mvn bnd-resolver:resolve
```

## Bndrun Details Inferred from Maven

The `-runee` and `-runrequires` values can be inferred from the maven project as follows:

  * `-runee`, if omitted from the bndrun file, will be inferred from the `<target>` configuration of `maven-compiler-plugin`
  * `-runrequires`, if omitted from the bndrun file, will be inferred by attempting to get the `Bundle-SymbolicName` (bsn) from the project's main artifact and if not found will use `artifactId`. The value will be applied as `osgi.identity;filter:='(osgi.identity=<bsn|artifactId>)'`, if the project packaging is `jar` or `war` and the project has the `bnd-maven-plugin`

## Implicit Repository

An *implicit repository* containing the project artifact and project dependencies (as defined through the configuration of `bundles`, `scopes`, `useMavenDependencies` and `includeDependencyManagement`) is created and added when this plugin is executed.

## Configuration Properties

| Configuration Property        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `bndruns`                     | Can contain `bndrun` child elements specifying a bndrun file to resolve. These are relative to the `${project.basedir}` directory. You can also specify `include` and `exclude` child elements using Ant-style globs to specify bndrun files in the `bndrunDir` directory.  _Defaults to `<include>*.bndrun</include>`._                                                                                                                                                               |
| `bndrunDir`                   | This directory will be used when locating bndrun files using `include` and `exclude`. _Defaults to `${project.basedir}`_.                                                                                                                                                                                                                                                                                                                                                              |
| `outputBndrunDir`             | The bndrun files will be written to the specified directory. If the specified directory is the same as `bndrunDir`, then any changes to a bndrun files will cause the bndrun file to be overwritten. _Defaults to `${project.basedir}`_.                                                                                                                                                                                                                                               |
| `failOnChanges`               | Whether to fail the build if any change in the resolved `-runbundles` is discovered. _Defaults to `true`._                                                                                                                                                                                                                                                                                                                                                                             |
| `writeOnChanges`              | Whether to write the resolved run bundles back to the `-runbundles` property of the `bndrun` file. _Defaults to `true`._                                                                                                                                                                                                                                                                                                                                                               |
| `bundles`                     | A collection of files to include in the *implicit repository*. Can contain `bundle` child elements specifying the path to a bundle. These can be absolute paths. You can also specify `include` and `exclude` child elements using Ant-style globs to specify bundles. These are relative to the `${project.basedir}` directory. _Defaults to dependencies in the scopes specified by the `scopes` property, plus the current artifact (if any and `useMavenDependencies` is `true`)._ |
| `useMavenDependencies`        | If `true`, adds the project dependencies subject to `scopes` to the collection of files to include in the *implicit repository*. _Defaults to `true`._                                                                                                                                                                                                                                                                                                                                 |
| `reportOptional`              | If `true`, resolution failure reports will include optional requirements. _Defaults to `true`._                                                                                                                                                                                                                                                                                                                                                                                        |
| `scopes`                      | Specify from which scopes to collect dependencies. _Defaults to `compile, runtime`._ Override with property `bnd.resolve.scopes`.                                                                                                                                                                                                                                                                                                                                                      |
| `includeDependencyManagement` | Include `<dependencyManagement>` subject to `scopes` when collecting files to include in the *implicit repository*. _Defaults to `false`._ Override with property `bnd.resolve.include.dependency.management`.                                                                                                                                                                                                                                                                         |
| `skip`                        | Skip the project. _Defaults to `false`._ Override with property `bnd.resolve.skip`.                                                                                                                                                                                                                                                                                                                                                                                                    |
