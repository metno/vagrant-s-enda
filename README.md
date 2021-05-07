# vagrant-s-enda

Starts the S-ENDA specific software in a one node Kubernetes cluster based on [`k3s`](https://github.com/k3s-io/k3s).

This is meant for testing ingestion of metadata locally. More information about the S-ENDA project on the documentation pages [Welcome to S-ENDA’s documentation!](https://s-enda-documentation.readthedocs.io/en/latest/).

## Getting started

To start the S-ENDA services. Just start the VM as follows.

```bash
vagrant up
```

You have now started a VM with services that you can access from you local machine on the IP `10.10.10.10`. This is a private address and not accessible from  other machines.

### Endpoints

These are the endpoint you have access to.

* [`10.10.10.10`](http://10.10.10.10) --- PyCSW API endpoint

TODO:

* [`10.10.10.10:8080`](http://10.10.10.10:8080) --- MMS API endpoint
* `10.10.10.10:4222 --- MMS NATS server endpoint

### Inside the VM

All commands can be run from the `vagrant` user inside the VM. Access the VM by entering it with `vagrant ssh`.

* [`docker`](https://docs.docker.com/engine/reference/commandline/cli/) --- used as backend for `k3s`.
* [`kubectl`](https://github.com/kubernetes/kubectl) --- to interact with Kubernets.
* [`k9s`](https://github.com/derailed/k9s) --- for a terminal menu based interaction with Kubernetes.

Permanent storage for Kubernetes is in subfolder under `/opt`.

## Usage examples

TODO

## Diagram

To connect the dots, we have a C4 diagram which describes how it all connects together.

![Deployment](dep.svg)

<!---
'plantuml -tsvg README.md

@startuml dep
!includeurl https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/v2.0.1/C4_Component.puml

LAYOUT_TOP_DOWN()
'LAYOUT_LEFT_RIGHT
LAYOUT_WITH_LEGEND()
'LAYOUT_AS_SKETCH()


'
' Persons
'
Person(internal_user, "User", "User of the S-ENDA Vagrant setup.")

'----------------------------------------------------------------------

System_Boundary(k8s_env, "Kubernetes environment") {

'
'
'

Container(dmci, "dcmi", "deployment, service, ingress", "<b>TODO:</b> API that receives MMD files, convert to ISO. Pushes ISO to PyCSW API, update s-enda-mmd-xml Git repository with new MMD file.")
'Rel_R(dmci, pycsw_service, "Upload ISO with API")


'
' ConfigMap
'
Component(catalog_service_postgis_conf, "catalog-service-postgis", "configmap", "passwd file for making PostGIS work in K8s.")
Component(pycsw_config, "pycsw-config", "configmap", "Common PyCSW configuration file.")

'
' Services
'
Component(catalog_service_postgis_service, "catalog-service-postgis", "service", "Service component exposing PostGIS on port 5432.")
Component(pycsw_service, "pycsw", "service", "Service component exposing PyCSW API on port 80.")
'
' Deployment catalog-service-postgis
'
Container_Boundary("catalog_service_postgis_boundary", "catalog-service-postgis deployment") {
  Component(init_db_directory, "init-db-directory", "alpine", "Initialize database directory.")
  Component(catalog_service_postgis, "catalog-service-postgis", "postgis/postgis", "Holds ISO data translated from MMD for PyCSW.")
  Rel_U(catalog_service_postgis, catalog_service_postgis_conf, "Mount passwd file")
}

ComponentDb(catalog_service_postgis_storage, "catalog-service-postgis", "pvc", "Permanent storage for PostGIS.")

Rel(catalog_service_postgis, catalog_service_postgis_storage, "Stores data in physical volume claim")

'
' Deployment pycsw
'
Container_Boundary("pycsw_boundary", "pycsw deployment") {
  Component(pycsw1, "pycsw", "geopython/pycsw", "Running instance of PyCSW.")

  Rel_U(pycsw1, pycsw_config, "Mount the config file")
}

Rel(pycsw1, catalog_service_postgis_service, "Reads data from PostGIS.")
Rel(catalog_service_postgis_service, catalog_service_postgis, "LB")

'
' Deployment pycsw-ingest
'

Rel(internal_user, pycsw_service, "Access CSW API via", "10.10.10.10:80")
Rel(internal_user, dmci, "Uploads MMD file via", "10.10.10.10:8000")
Rel_L(dmci, pycsw_service,"Forwards converted metadata to")
Rel(pycsw_service, pycsw1, "LB")

}

@enduml
-->


<!---
vim: set spell spelllang=en:
-->
