# PHP 7.2 Base Docker image

This is the base PHP image used as parent image for apache & cli images, built from Debian Jessie

This image should be used as Base image in Dockerfiles needing php.

Example from apache_php72 Dockerfile: 


    FROM klabs/php72
    LABEL apache.version=2.4
