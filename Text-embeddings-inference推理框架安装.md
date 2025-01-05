# Text-embeddings-inference推理框架安装

## 一、安装Cargo

``` bash
curl https://sh.rustup.rs -sSf | sh
```

完成后需要根据提示配置运行路径，并执行cargo获得帮助信息表示安装成功

## 二、安装编译需要的依赖

``` bash
sudo apt-get install libssl-dev gcc -y
```

## 三、Clone Text-embeddings-inference 项目库

``` bash
git clone https://github.com/huggingface/text-embeddings-inference.git

cd text-embeddings-inference
```

## 四、编译

``` bash
# On x86 only for CPU
cargo install --path router -F mkl
# On M1 or M2
cargo install --path router -F metal
# On Ampere and Hopper
cargo install --path router -F candle-cuda -F http --no-default-features
```

## 五、推理测试

``` bash
model=BAAI/bge-reranker-large

text-embeddings-router --model-id $model --port 8080
```

## 六、源代码修改

``` rust
# backends/candle/src/compute_cap.rs

# fix 1
fn compute_cap_matching(runtime_compute_cap: usize, compile_compute_cap: usize) -> bool {
    match (runtime_compute_cap, compile_compute_cap) {
        (75, 75) => true,
        (80..=89, 80) => true,
        (86..=89, 80..=86) => true,
        (87, 87) => true, // 新增 87 的支持
        (89, 89) => true,
        (90, 90) => true,
        (_, _) => false,
    }
}

# fix 2
#[cfg(test)]
mod tests {
    use crate::compute_cap::compute_cap_matching;

    #[test]
    fn test_compute_cap() {
        assert!(compute_cap_matching(75, 75));
        assert!(compute_cap_matching(80, 80));
        assert!(compute_cap_matching(86, 86));
        assert!(compute_cap_matching(89, 89));
        assert!(compute_cap_matching(90, 90));

        assert!(compute_cap_matching(86, 80));
        assert!(compute_cap_matching(89, 80));
        assert!(compute_cap_matching(89, 86));
        assert!(compute_cap_matching(87, 87)); // 新增 87 的测试用例

        assert!(!compute_cap_matching(75, 80));
        assert!(!compute_cap_matching(75, 86));
        assert!(!compute_cap_matching(75, 89));
        assert!(!compute_cap_matching(75, 90));

        assert!(!compute_cap_matching(80, 75));
        assert!(!compute_cap_matching(80, 86));
        assert!(!compute_cap_matching(80, 89));
        assert!(!compute_cap_matching(80, 90));

        assert!(!compute_cap_matching(86, 75));
        assert!(!compute_cap_matching(86, 89));
        assert!(!compute_cap_matching(86, 90));

        assert!(!compute_cap_matching(89, 75));
        assert!(!compute_cap_matching(89, 90));

        assert!(!compute_cap_matching(90, 75));
        assert!(!compute_cap_matching(90, 80));
        assert!(!compute_cap_matching(90, 86));
        assert!(!compute_cap_matching(90, 89));

        assert!(!compute_cap_matching(87, 75)); // 新增 87 的测试用例
        assert!(!compute_cap_matching(87, 80));
        assert!(!compute_cap_matching(87, 86));
        assert!(!compute_cap_matching(87, 89));
        assert!(!compute_cap_matching(87, 90));
    }
}
```

重新编译，并测试

## 七、rerank 服务测试

``` bash
curl 127.0.0.1:8080/rerank \
    -X POST \
    -d '{"query": "What is Deep Learning?", "texts": ["Deep Learning is not...", "Deep learning is..."]}' \
    -H 'Content-Type: application/json'
```

### 服务日志

``` bash
2025-01-04T20:38:18.706787Z  INFO text_embeddings_backend_candle: backends/candle/src/lib.rs:292: Starting FlashBert model on Cuda(CudaDevice(DeviceId(1)))
2025-01-04T20:38:31.539445Z  INFO text_embeddings_router: router/src/lib.rs:248: Warming up model
2025-01-04T20:38:32.189069Z  INFO text_embeddings_router::http::server: router/src/http/server.rs:1812: Starting HTTP server: 0.0.0.0:8080
2025-01-04T20:38:32.189098Z  INFO text_embeddings_router::http::server: router/src/http/server.rs:1813: Ready
2025-01-04T20:44:11.047170Z  INFO rerank{total_time="177.15121ms" tokenization_time="727.783µs" queue_time="79.024583ms" inference_time="87.618256ms"}: text_embeddings_router::http::server: router/src/http/server.rs:459: Success
```

## 问题处理

1. 需要确保显卡驱动、cuda运行时、cuda编译器被正确安装并且在环境变量路径中可以找到
2. 编译过程及其漫长、内存占用超过90%
3. 不支持SM87架构，修改源代码重新编译