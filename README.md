# Supply-chain-mangement
clear;clc;
alpha=0.50; %价值判断[0.7 ]
c=0.20; %成本
gama=0.2; %第一阶段不是产品需求，在第二阶段转化为新的需求的比例
epsi=2; %顾客对于新产品的好奇心[2 ]
ep=1*10^(-5);
betaa=0.8;%第二阶段市场对产品的最大估计值[0.8 ]c:0.01:1
lamda1=0.3;%顾客对于企业1产品的认可程度(基于第一阶段具有负体验消费)[0.4 ]
lamda2=1.5;%顾客对于企业1产品的认可程度(基于第一阶段具有正体验消费)[4 ]
% beta1=lamda1*betaa;
% if lamda2*betaa<1
%     beta2=lamda2*betaa;%第二阶段具有正体验消费者对产品的最大估计值
% else
%     beta2=1;
% end
p1=0.3;%第一阶段产品企业1产品定价[0.4 ]
p1_ba=c;%第二阶段企业1的产品定价[0.2 ]

% v=0.3;% v=0:0.01:betaa;;%市场对产品的估计值
% v1=lamda1*v;% 负消费体验顾客对产品的估值
% v2=lamda1*v;% 正消费体验顾客对产品的估值
Q1=p1/alpha;%第一阶段结束后未购买企业1产品的消费者数量
Q2=p1*(alpha-p1)/alpha^2;%第一阶段结束后购买了企业1产品的消费者中具有负体验感的消费数量
Q3=((alpha-p1)/alpha)^2;%第一阶段结束后购买了企业1产品的消费者中具有正体验感的消费数量
D_pi=1;
M=1;
cc=c;
op_p1_ba=zeros(1,3);
p2_ba=0.3:0.0001:0.8;%第二阶段企业2的产品定价(新产品)
for i=1:size(p2_ba,2)
    p11_ba(i)=solve_1(D_pi,ep,betaa,epsi,p2_ba(i),p1_ba,lamda2,lamda1,Q1,Q2,Q3,cc,M);
    p12_ba(i)=solve_2(D_pi,ep,betaa,epsi,p2_ba(i),p1_ba,lamda2,lamda1,Q1,Q3,cc,M);
    p13_ba(i)=solve_3(D_pi,ep,betaa,epsi,p2_ba(i),p1_ba,lamda2,Q3,cc,M);
    
    D11=Q1/betaa*(betaa-p11_ba(i))+Q2/betaa*(min(betaa,(epsi*p2_ba(i)-p11_ba(i))/(epsi-lamda1))-p11_ba(i))+Q3/betaa*(min(betaa,(epsi*p2_ba(i)-p11_ba(i))/(epsi-lamda2))-p11_ba(i));
    Pro11(i)=p11_ba(i)*D11;
    
    D12=Q1/betaa*(betaa-p12_ba(i))+Q3/betaa*(min(betaa,(epsi*p2_ba(i)-p12_ba(i))/(epsi-lamda2))-p12_ba(i));
    Pro12(i)=p12_ba(i)*D12;
    
    D13=Q3/betaa*(min(betaa,(epsi*p2_ba(i)-p13_ba(i))/(epsi-lamda2))-p13_ba(i));
    Pro13(i)=p13_ba(i)*D13;
    op_p1_ba(i,1:3)=[p11_ba(i) p12_ba(i) p13_ba(i)];
    A(i,1:3)=[Pro11(i) Pro12(i) Pro13(i)];  
    [x,y]=max(A(i,:),[],2);
    Loc(i,:)=[i y];
    opt_price(i)=op_p1_ba(i,y);
    opt_Pro(i)=A(i,y);
end
A1=[p2_ba' opt_Pro'];
plot(p2_ba,opt_Pro,'r');
% legend(['{\pi^* }'],'interpreter','latex');
grid on;
% title('(1)');
xlabel(['$\bar p_2 $'],'interpreter','latex');ylabel('\pi^*');%axis([0.3 0.5 0.12 0.16]);
