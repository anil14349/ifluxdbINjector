# ifluxdbInjector

package com.example.demo;


import java.time.Instant;
import java.util.List;

import com.influxdb.annotations.Column;
import com.influxdb.annotations.Measurement;
import com.influxdb.client.InfluxDBClient;
import com.influxdb.client.InfluxDBClientFactory;
import com.influxdb.client.WriteApi;
import com.influxdb.client.domain.WritePrecision;
import com.influxdb.client.write.Point;
import com.influxdb.query.FluxTable;

public class DemoApplication {

	@Measurement(name = "mem")
	public static class Mem {
		@Column(tag = true)
		String host;
		@Column
		Double used_percent;
		@Column(timestamp = true)
		Instant time;
	}

	public static void main(String[] args) {

		String token = "NYLjUVZf37Y0_3pN4fVMYSDnyF30_tW5tLlW0LTL_172aGKJ6e3Ynhdn-ch3TDPNmzZJOS5_EdIBiP043ESb8g==";
		String bucket = "initialBucket";
		String org = "hsbc";

		InfluxDBClient client = InfluxDBClientFactory.create("http://localhost:8086", token.toCharArray());

		System.out.println(client);

		String data = "mem,host=host1 used_percent=23.43234543";
		try (WriteApi writeApi = client.getWriteApi()) {
			writeApi.writeRecord(bucket, org, WritePrecision.NS, data);
		}

		/**
		 * 
		 */

		Mem mem = new Mem();
		mem.host = "host1234";
		mem.used_percent = 110.43234543;
		mem.time = Instant.now();

		try (WriteApi writeApi = client.getWriteApi()) {
			writeApi.writeMeasurement(bucket, org, WritePrecision.NS, mem);
		}

		/**
		 * 
		 */

		Point point = Point.measurement("mem").addTag("host", "host1").addField("used_percent", 23.43234543)
				.time(Instant.now(), WritePrecision.NS);

		try (WriteApi writeApi = client.getWriteApi()) {
			writeApi.writePoint(bucket, org, point);
		}

		/**
		 * 
		 */

		String query = String.format("from(bucket: \"%s\") |> range(start: -1h)", bucket);
		List<FluxTable> tables = client.getQueryApi().query(query, org);
		System.out.println(tables);


	}

}


###################################################################

<dependency>
	<groupId>com.influxdb</groupId>
	<artifactId>influxdb-client-java</artifactId>
	<version>2.0.0</version>
</dependency>
