---
title: "react-image-file-resizer 모듈 사용하여 이미지 최적화하기"
date: "2024-09-07"
thumbnail: "/assets/img/thumbnail/1.jpeg"
---

# [react-image-file-resizer 모듈 사용하여 이미지 최적화하기]

## 이미지 최적화를 진행해야하는 이유

프로젝트에서 이미지를 업로드할 때 S3 Presigned URL을 사용했으나, 최적화 없이 크기가 큰 이미지를 그대로 S3에 업로드하면서, 화면에 여러 이미지를 로드해야 하는 서비스에서는 이미지 로딩 속도가 크게 저하되는 문제가 발생했습니다. 이를 해결하기 위해 이미지 최적화를 진행하게 되었습니다.

또한, 웹 페이지 성능을 향상시키기 위해서는 이미지 파일의 최적화가 필수적입니다. 최적화되지 않은 큰 이미지는 페이지 로딩 속도를 저하시킬 수 있으며, 이는 사용자 경험과 SEO에도 부정적인 영향을 줄 수 있습니다. 클라이언트 측에서 이미지를 리사이징하는 방법이 효과적입니다.

이번 포스트에서는 react-image-file-resizer 모듈을 사용하여 어떻게 간편하게 이미지를 최적화하고 리사이즈할 수 있는지 실제 프로젝트를 기반으로 설명하겠습니다.

## 어떻게 최적화해야할지??

이미지를 최적화하는 방법 중 하나는 클라이언트 측에서 직접 이미지를 리사이즈하는 것입니다. React 프로젝트에서 이를 손쉽게 구현할 수 있는 도구가 바로 react-image-file-resizer 모듈입니다. 이 모듈을 사용하면 사용자가 업로드한 이미지를 지정된 크기와 품질로 간편하게 변환할 수 있습니다.

또한 WEBP(웹피) 라는 확장자를 이용하여 더 작은 용량으로 이미지를 저장하할 수 있도록 합니다.

이번 글에서는 react-image-file-resizer 모듈을 사용하여 클라이언트 측에서 이미지를 리사이즈하고 최적화하는 방법을 소개합니다. 이미지 업로드가 많은 웹 애플리케이션에서 성능을 높이는 데 유용한 팁도 함께 살펴보겠습니다.

### WebP 확장자의 장점

WebP는 구글이 개발한 이미지 파일 포맷으로, Jpeg 및 Png 파일을 대체할 수 있는 포맷입니다. WebP는 이미지 품질을 유지하면서도 더 작은 용량으로 저장할 수 있으며, 구글에 따르면 동일한 품질의 Jpeg 또는 Png 파일보다 약 27%~34% 적은 용량을 차지합니다. 이를 통해 이미지 최적화는 물론, 페이지 로딩 시간 단축과 검색 엔진 최적화(SEO)에도 긍정적인 영향을 줄 수 있습니다.

이제 모듈 설치부터 예시코드, 실제 활용코드까지 함께 작성해보겠습니다.

### 필수 모듈 설치

저는 react 17을 사용하고 있고, 가장최신 버전인 0.4.8 버전을 사용했습니다.

<code>npm install react-image-file-resizer</code>

### 기본 사용 예

이미지 리사이징을 위해 먼저 resizeFile 함수를 만들어야 합니다. 이 프로젝트는 TypeScript 기반으로 진행되었기 때문에 우선 ImgFileResizer라는 타입을 정의했고, 이를 통해 Props로 커스텀 값을 넘겨받을 수 있게 설정했습니다.

필수 값 및 필요한 값들만 명시하였으며, 각 속성에 대한 자세한 설명은 [여기](https://www.npmjs.com/package/react-image-file-resizer)를 참조해주세요.

```typescript
type ImgFileResizer = {
  maxWidth: number;
  maxHeight?: number;
  compressFormat: "JPEG" | "PNG" | "WEBP";
  quality: number;
  rotation: number;
  outputType: "base64" | "blob" | "file";
  minWidth?: number;
  minHeight?: number;
};

const resizeFile = (file: File, imgFileResizer: ImgFileResizer) =>
  new Promise((resolve) => {
    const img = new Image();
    img.src = URL.createObjectURL(file);

    img.onload = () => {
      const aspectRatio = img.height / img.width;
      const height = aspectRatio * imgFileResizer.maxWidth;

      Resizer.imageFileResizer(
        file,
        imgFileResizer.maxWidth,
        imgFileResizer.maxHeight || height,
        imgFileResizer.compressFormat,
        imgFileResizer.quality,
        imgFileResizer.rotation,
        (uri) => {
          resolve(uri);
        },
        imgFileResizer.outputType,
        imgFileResizer.minWidth,
        imgFileResizer.minHeight
      );
    };
  });
```

### 실제 프로젝트에 적용해보기

이미지를 쉽게 리사이징 할 수 있도록 저는 FileUploadInput이라는 커스텀 컴포넌트를 프로젝트에 추가해, 이미지를 자동으로 리사이징하고 S3에 업로드할 수 있도록 했습니다.

FileUploadInput은 TypeScript 기반으로 작성되었으며, imgFileResizer라는 옵션을 통해 리사이징 설정을 커스터마이즈할 수 있습니다. 이 컴포넌트는 이미지를 업로드할 때 리사이징을 자동으로 처리하여 S3 Presigned URL을 통해 최적화된 이미지를 S3에 업로드합니다.

### 실제 예시

```typescript
interface FileUploadInputProps
  extends Omit<MsaCrFileUploadInputProps, "status"> {
  refResourceType: RefResourceType;
  s3ResourceContentType: S3ResourceContentType;
  imgFileResizer?: ImgFileResizer;
  onUploaded: (presignedUrl: S3PresignedUrl & { fileName: string }) => void;
}

export function FileUploadInput({
  refResourceType,
  s3ResourceContentType,
  disabled,
  imgFileResizer,
  onUploaded,
  ...props
}: FileUploadInputProps) {
  const [fileUploadStatus, setFileUploadStatus] = useState<FileUploadStatus>(
    FileUploadStatus.Wait
  );
  const fileRef = useRef<File | null>(null);
  const fileKey = useRef<string | null>(null);

  const { mutate, isLoading, isError, isSuccess, data } =
    usePresignedPostUrlMutation(
      refResourceType,
      getUseId(),
      s3ResourceContentType,
      fileRef.current?.name || "test"
    );

  useEffect(() => {
    if (isLoading) setFileUploadStatus(FileUploadStatus.Uploading);

    if (isError) setFileUploadStatus(FileUploadStatus.Error);

    if (isSuccess && data) {
      if (!fileRef.current) return;

      setFileUploadStatus(FileUploadStatus.Done);

      const xhr = new XMLHttpRequest();
      xhr.open("PUT", data.presignedUrl.url, true);
      xhr.setRequestHeader("Content-Type", fileRef.current?.type);
      xhr.onload = () => {
        if (xhr.status === 200) {
          fileKey.current = data.presignedUrl.key;

          onUploaded({
            ...data.presignedUrl,
            fileName: fileRef.current?.name || "",
          });
        } else {
          setFileUploadStatus(FileUploadStatus.Error);
        }
      };
      xhr.send(fileRef.current);
    }
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [isLoading, isError, isSuccess, data]);

  return (
    <커스텀인풋
      {...props}
      disabled={disabled || isLoading}
      status={fileUploadStatus}
      errorMessage={
        fileUploadStatus === FileUploadStatus.Error
          ? "The file upload request has failed."
          : ""
      }
      onFileChange={async (file) => {
        if (!file) return;

        // 이미지 파일 리사이징 처리
        if (file.type.split("/")[0] === "image" && !!imgFileResizer) {
          const compressedFile = (await resizeFile(
            file,
            imgFileResizer
          )) as File;

          fileRef.current = compressedFile;
        } else {
          fileRef.current = file;
        }

        setFileUploadStatus(FileUploadStatus.Uploading);

        mutate();
      }}
    />
  );
}
```

### 후기

실제 프로젝트에 적용해본 결과, 셋업 과정은 매우 간단했고, 성능 개선도 확실하게 이루어졌습니다. 예를 들어, 2.4MB 크기의 이미지를 381KB로 압축하여 약 84%의 크기 감소를 달성했으며, 그에 따라 페이지 로딩 시간도 크게 단축되었습니다. 이러한 성능 개선 덕분에 사용자 경험이 크게 향상되었음을 체감할 수 있었습니다.

다만 한 가지 불편했던 점은 maxHeight 값이 필수로 입력되어야 한다는 점입니다. 물론, 이미지 최적화에서 maxWidth와 maxHeight를 명시하는 것이 중요하지만, maxHeight를 자동으로 비율에 맞춰 조정할 수 있는 옵션이 있었다면 더 유연하게 사용할 수 있었을 것입니다. 특히 모든 이미지의 높이를 일일이 지정하지 않고, 폭에 맞춰 자동으로 비율이 조정되었더라면 작업 효율이 더욱 높아졌을 것이라고 생각합니다.

그럼에도 불구하고, react-image-file-resizer는 이미지 최적화 작업에서 매우 강력하고 편리한 도구임을 확인할 수 있었으며, 앞으로도 성능 최적화를 위해 지속적으로 사용할 계획입니다.

### **결론**

react-image-file-resizer 모듈을 사용하면 클라이언트 측에서 효율적으로 이미지를 리사이즈할 수 있어 웹 성능을 향상시키는 데 큰 도움이 됩니다. 클라이언트 측에서 이미지를 리사이징하여 서버 리소스를 절약하고, 페이지 로딩 속도를 크게 향상시킬 수 있기 때문에, 꼭 한 번 사용해보시길 권장드립니다.

궁금한 점이나 추가적인 도움이 필요하다면 댓글로 남겨주세요!
