---
title: SpringBoot学习笔记
date: 2018/4/20 16:28:01
tags:
categories: [编程, Java]
---

### 1. actuator  
#### 1.1 health
@Component
public class ExampleHealthIndicator implements HealthIndicator {

	@Override
	public Health health() {
		return Health.up().withDetail("counter", 42).build();
	}

}

#### 1.2 info
@Bean
	public HealthIndicator helloHealthIndicator() {
		return new HealthIndicator() {

			@Override
			public Health health() {
				return Health.up().withDetail("hello", "world").build();
			}

		};
	}
