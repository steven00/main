

//REFERENCE:
//this code is a modified version from the book "INTRODUCTION TO 3D GAME Programming with DirectX 9.0 by Frank Luna
//code changed and modified by stephan vinent
// Date modified: 1/21/2016


#include"graphics.h"

HWND hwnd = 0;
IDirect3DDevice9* dev = 0;
void direct(IDirect3D9 *dir);
mesh m;
IDirect3D9 *dir;
vector<D3DMATERIAL9> material01(0);
vector<IDirect3DTexture9*> texture01(0);
ID3DXMesh* model01 = 0;
D3DXMATRIX projection;
terrain *terrains;
graphics run;

LRESULT CALLBACK winProc(HWND hwnd,UINT message,WPARAM w,WPARAM l);
int WINAPI WinMain(HINSTANCE h, HINSTANCE p, PSTR cmd, int show)
{
	
	WNDCLASS win;

	win.cbClsExtra = 0;
	win.cbWndExtra = 0;
	win.hInstance = h;
	win.hIcon = LoadIcon(0,IDI_APPLICATION);
	win.hCursor = LoadCursor(0,IDC_ARROW);
	win.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	win.lpszClassName = "DirectCore";
	win.lpfnWndProc = (WNDPROC)winProc;
	win.lpszMenuName = 0;
	win.style = CS_HREDRAW |CS_VREDRAW;

	RegisterClass(&win);

	

	hwnd = CreateWindow("DirectCore","DirectCore",WS_EX_TOPMOST,0,0,640,480,0,0,h,0);

	ShowWindow(hwnd,SW_SHOW);
	UpdateWindow(hwnd);


	direct(dir);
	
	m.loadXfiles(dev, material01, texture01, model01);
	m.XfilesGraphics(dev);

	terrains = new terrain(dev,"Assets/heightmap.raw",64,64,10,0.5);

	
	terrains->texture(dev,64,64,0.5);

	MSG mess;

	ZeroMemory(&mess,sizeof(MSG));

	static float finishEvent = 0;

	while(mess.message != WM_QUIT)
	{
		if(PeekMessage(&mess,0,0,0,PM_REMOVE))
		{
			TranslateMessage(&mess);
			DispatchMessage(&mess);
		}
		else
		{
			float currentEvent = 0;
			float dt = 0;

			run.render(dt,dev,*terrains,material01,texture01,model01, h, hwnd);

			finishEvent = currentEvent;
		}

	}
		return mess.wParam;


}






void direct(IDirect3D9 *dir)
{
	dir = 0;

	dir = Direct3DCreate9(D3D_SDK_VERSION);

	D3DCAPS9 caps;

	dir->GetDeviceCaps(D3DADAPTER_DEFAULT,D3DDEVTYPE_HAL,&caps);

	int vertexPixels = D3DCREATE_HARDWARE_VERTEXPROCESSING;

	D3DPRESENT_PARAMETERS dp;

	dp.AutoDepthStencilFormat = D3DFMT_D24S8;
	dp.BackBufferWidth = 640;
	dp.BackBufferHeight = 480;
	dp.BackBufferCount = 1;
	dp.BackBufferFormat = D3DFMT_A8R8G8B8;
	dp.EnableAutoDepthStencil = true;
	dp.Flags = 0;
	dp.FullScreen_RefreshRateInHz = D3DPRESENT_RATE_DEFAULT;
	dp.hDeviceWindow = hwnd;
	dp.MultiSampleQuality = 0;
	dp.MultiSampleType = D3DMULTISAMPLE_NONE;
	dp.PresentationInterval = D3DPRESENT_INTERVAL_IMMEDIATE;
	dp.SwapEffect = D3DSWAPEFFECT_DISCARD;
	dp.Windowed = true;


	 dir->CreateDevice(D3DADAPTER_DEFAULT,D3DDEVTYPE_HAL,hwnd,vertexPixels,&dp,&dev);
	
}



LRESULT CALLBACK winProc(HWND hwnd,UINT message,WPARAM w,WPARAM l)
{
	switch(message)
	{
	case WM_DESTROY:
		PostQuitMessage(0);
		break;
	
	case WM_KEYDOWN:
		/*if(w == VK_ESCAPE);
		DestroyWindow(hwnd);*/
		break;
	}
	return DefWindowProc(hwnd,message,w,l);

}
