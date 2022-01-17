---
layout: post
title:  "มาลองเล่นเจ้า gRPC กันเถอะ"
categories: [gRPC]
tags: [Tutorial, RPC framework]
description: "gRPC คืออะไร"
featured: true
hidden: true
---

พอดีได้มีโอกาสศึกษาเรื่อง gRPC โดยได้รับมอบภาระกิจจากพี่ ๆ ในทีมให้มาลองศึกษาเรื่องนี้ เอาละวะไหน ๆ ก็ศึกษามันละ ขอเขียนลงไว้ใน Blog ซักหน่อยละกันปีละบทความก็ยังดี 555

gRPC เป็น open source ตัวนึงที่ถูกพัฒนาโดย Google จะใช้ Protocol HTTP/2 และใช้ Payload Protocol Buffers เป็นตัวส่งขอมูลโดนเจ้าตัว Protobuf จะส่งข้อมูลในรูปแบบของ binary
ทำให้การส่งข้อมูลเร็วกว่า XML และ JSON เพราะข้อมูลที่ส่งกันไปมาโดนบีบอัดให้เล็กลง

หน้าที่ของมันคือเป็นสื่อกลางระหว่างระบบต่าง ๆ รองรับภาษาที่หลากหลายทั้ง C++, Java, PHP, Go, Node, Ruby, Python, C#

เพิ่ม maven dependencies ใน pom.xml

    {% highlight xml %}
    
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-netty</artifactId>
        <version>1.42.0</version>
    </dependency>
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-protobuf</artifactId>
        <version>1.42.0</version>
    </dependency>
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-stub</artifactId>
        <version>1.42.0</version>
    </dependency>
    {% endhighlight %}

เพิ่ม mvn plugin สำหรับการ generate code จากไฟล์ .proto ไปเป็น java class

    {% highlight xml %}
    
    <build>
        <extensions>
            <extension>
                <groupId>kr.motd.maven</groupId>
                <artifactId>os-maven-plugin</artifactId>
                <version>1.7.0</version>
            </extension>
        </extensions>
        <plugins>
            <plugin>
                <groupId>org.xolstice.maven.plugins</groupId>
                <artifactId>protobuf-maven-plugin</artifactId>
                <version>0.6.1</version>
                <configuration>
                    <protocArtifact>com.google.protobuf:protoc:3.17.3:exe:${os.detected.classifier}</protocArtifact>
                    <pluginId>grpc-java</pluginId>
                    <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.42.0:exe:${os.detected.classifier}</pluginArtifact>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>compile-custom</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    {% endhighlight %}

สร้างไฟล์ .proto (Protocol Buffers) ภายใต้ src/main/proto

    {% highlight proto %}
    
    //ระบุ syntax version ของ Protocol Buffers
    syntax = "proto3";
    //ถ้าค่าเป็น true เมื่อ compile จะได้ classes/enums/etc. แยกออกจากกันไม่รวมอยู่ใน class เดียวกันทั้งหมด
    option java_multiple_files = true;
    //กำหนดชื่อ package ที่จะสร้าง classes/enums/etc. เมื่อ compile
    package me.doopdip.grpc;
    
    //กำหนดโครงสร้างของ request ที่จะรับเข้ามา
    message CarRequest {
        string model = 1;
    }
    
    //กำหนดโครงสร้าง response ที่จะส่งออกไป
    message CarResponse {
        int32 price = 1;
    }
    
    //กำหนด service definition
    service CarService {
        rpc detail(CarRequest) returns (CarResponse);
    }
    {% endhighlight %}


**ทำการรันคำสั่ง `mvn compile` ก่อนหนึ่งครั้งเพื่อทำการ compiler ตัว protocol buffers ไปเป็น java class**

หลังจาก generate code จากไฟล์ .proto มาแล้วก็จะทำการสร้าง service โดยทำการ extends CarServiceImplBase ที่ได้จากการ generate มา override
method detail เพื่อทำการ operation

    {% highlight java %}
    
    public class CarService extends CarServiceImplBase {
    
        //ทำการ override detail method ที่เราประกาศไว้ใน file car.proto
        @Override
        public void detail(CarRequest request, StreamObserver<CarResponse> responseObserver) {
            int price = 0;
    
            if ("TESLA_MODEL_S".equals(request.getModel())) {
                price = 1000000;
            } else if ("TESLA_MODEL_X".equals(request.getModel())) {
                price = 2000000;
            }
    
            //สร้าง instance ของ response และ set ค่า field ต่าง ๆ โครงสร้างของ car response ที่กำหนดไว้ใน car.proto
            CarResponse response = CarResponse.newBuilder().setPrice(price).build();
    
            //set response object เพื่อที่จะ callback กลับไปให้ฝั่ง client ที่เรียกเข้ามา
            responseObserver.onNext(response);
            //เรียก onCompleted เพื่อบอก client ว่าสิ้นสุดการส่งข้อมูลแล้ว (ปิด stream)
            responseObserver.onCompleted();
        }
    }
    {% endhighlight %}


สร้าง gRPC Server เพื่อที่ระรอรับ request จาก client

    {% highlight java %}
    
    public class GrpcServer {
        private final static int PORT = 8888;
    
        public static void main(String[] args) throws IOException, InterruptedException {
            //สร้าง gRPC server กำหนด port ไว้ที่ 8888 และ add service ที่สร้างไว้ก่อนหน้าที่ก็ตือ CarService
            Server server = ServerBuilder.forPort(PORT).addService(new CarService()).build();
            //เริ่ม start sever
            server.start();
    
            System.out.println("Server started on port: " + PORT);
    
            //ทำการเรียก await termination เพื่อให้ idle server รอไว้
            server.awaitTermination();
        }
    }
    {% endhighlight %}


สร้าง client เพื่อที่จะทดสอบการส่ง request ไปยัง server

    {% highlight java %}

    public class GrpcClient {
        public static void main(String[] args) {
            //สร้าง channel โดยกำหนด ip address และ port ของ server ที่จะติดต่อ
            ManagedChannel channel = ManagedChannelBuilder.forAddress("localhost", 8888).usePlaintext().build();
    
            //สร้าง stub เป็นช่องทาง remote call ไป server
            CarServiceGrpc.CarServiceBlockingStub stub = CarServiceGrpc.newBlockingStub(channel);
    
            String model = "TESLA_MODEL_X";
            System.out.println("Request car model: " + model);
    
            //สร้าง instance ของ request และ set ค่า field ต่าง ๆ โครงสร้างของ car request ที่กำหนดไว้ใน car.proto
            CarRequest request = CarRequest.newBuilder().setModel(model).build();
            //ทำการ call detail operation ไปยังฝั่ง server
            CarResponse response = stub.detail(request);
    
            System.out.println("Response price: " + response.getPrice());
    
            channel.shutdown();
        }
    }
    {% endhighlight %}
