# java-restapi
filters, namebinding, CORS:- https://www.baeldung.com/jersey-filters-interceptors, https://www.baeldung.com/cors-in-jax-rs


#Filters:
/*
* To change this license header, choose License Headers in Project Properties.
* To change this template file, choose Tools | Templates
* and open the template in the editor.
*/
package com.chessvision.rest.jaxrs.filters;

import java.io.IOException;
import javax.ws.rs.container.ContainerRequestContext;
import javax.ws.rs.container.ContainerRequestFilter;
import javax.ws.rs.core.Response;
import javax.ws.rs.ext.Provider;
import com.chessvision.rest.jaxrs.bindings.AuthTokenBinding;
import com.chessvision.rest.jaxrs.service.AuthTokenService;
import java.util.List;
import java.util.Map;
import javax.ws.rs.core.MultivaluedMap;

/**
*
* @author praveencastelino
*/
@Provider
@AuthTokenBinding
public class AuthRequestFilter implements ContainerRequestFilter {

   @Override
   public void filter(ContainerRequestContext crc) throws IOException {
       
       String token = crc.getHeaderString("Authorization");
       if (token == null) {
           Response res = Response.status(Response.Status.UNAUTHORIZED).entity("Auth header missing").build();
           crc.abortWith(res);
           return;
       }

       String verfiedValue = AuthTokenService.getInstance().verifyToken(token);
       if (verfiedValue == null) {
           Response res = Response.status(Response.Status.UNAUTHORIZED).entity("Invalid auth header").build();
           crc.abortWith(res);
           return;
       } else {
           crc.getHeaders().add("PLAYER_ID", verfiedValue);
       }
   }

}
========================================================================
/*
* To change this license header, choose License Headers in Project Properties.
* To change this template file, choose Tools | Templates
* and open the template in the editor.
*/
package com.chessvision.rest.jaxrs.bindings;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import javax.ws.rs.NameBinding;

/**
*
* @author praveencastelino
*/

@NameBinding
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(value = RetentionPolicy.RUNTIME)
public @interface AuthTokenBinding {
}
==================================================================================================
package com.chessvision.rest.jaxrs.api;

import com.chessvision.rest.jaxrs.bindings.AuthTokenBinding;
import com.chessvision.rest.jaxrs.model.InlineResponse400;
import com.chessvision.rest.jaxrs.model.InlineResponse200Statistics;
import com.chessvision.rest.jaxrs.service.StatsService;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import java.math.BigDecimal;

import javax.ws.rs.*;
import javax.ws.rs.core.Response;

import io.swagger.annotations.*;
import java.sql.SQLException;

import java.util.List;
import javax.naming.NamingException;
import javax.ws.rs.core.Context;
import javax.ws.rs.core.HttpHeaders;
import javax.xml.datatype.DatatypeConfigurationException;

@Path("/stats")

@Api(description = "the stats API")
@Consumes({"application/json"})
@Produces({"application/json"})
@AuthTokenBinding
@javax.annotation.Generated(value = "class io.swagger.codegen.languages.JavaJAXRSSpecServerCodegen", date = "2018-07-19T15:19:12.384+05:30")

public class StatsApi {

   private final StatsService statsService;
   private final Gson gson;

   public StatsApi() {
       statsService = StatsService.getInstance();
       gson = new GsonBuilder().create();
   }

   @GET
   @Consumes({"application/json"})
   @Produces({"application/json"})
   @ApiOperation(value = "Fetch the stats for a level.", notes = "Fetch the stats for a level.", response = InlineResponse200Statistics.class, tags = {})
   @ApiResponses(value = {
       @ApiResponse(code = 200, message = "Stats about the worlds exposed to the user", response = InlineResponse200Statistics.class)
       ,
       @ApiResponse(code = 400, message = "Bad Request. The server cannot or will not process the request due to an apparent client error (malformed request syntax, etc)", response = InlineResponse200Statistics.class)
       ,
       @ApiResponse(code = 401, message = "Unauthorized. Authentication required", response = InlineResponse200Statistics.class)
       ,
       @ApiResponse(code = 403, message = "Forbidden. Insufficient permissions for resource", response = InlineResponse200Statistics.class)
       ,

