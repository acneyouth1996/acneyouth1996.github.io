---
layout: post
comments: true
mathjax: true
priority: 440000
title: "Neural ODEs in Detail"
tags: [Machine Learning]
excerpt: Machine Learning
img: ri.jpg
date: 2019-10-01 00:00:00
---

## ODE recap
1. Vector $$\mathbf{z}$$ changes in time
2. Derivative wrt. time $$ \frac{d \mathbf{z}}{d t}=f(\mathbf{z}(t), t)$$
3. Initial-value problem: what we usually do with ode is given $$\mathbf{z}(t_{0})$$, try to find
$$
\mathbf{z}\left(t_{1}\right)=\mathbf{z}\left(t_{0}\right)+\int_{t_{0}}^{t_{1}} f(\mathbf{z}(t), t, \theta) d t
$$ 
4. Euler methods to solver ode: Euler methods is the simplest ode solver,
$$
\mathbf{z}(t+h)=\mathbf{z}(t)+h f(\mathbf{z}, t)
$$




## Similarity between Residual Block and Euler methods

As you can see from the above equation, there is a some similarity between euler methods and residual block design.

Example of ResNet:

```python
def resnet(z, theta):
    for t in [1:T]:
        z = z + f(z,t,theta)
    return z
```

The key idea of ODE-Net is to make hidden states continuously defined with depth, in other words, use a 
ODE solver to replace the whole for loop in resnet.

```python
def odenet(z, t, theta):
    # zero and one is the start time and end time
    return ODESolve(f, z , 0, 1, theta)
    
```


## How to train ODE net?


The objective we want to optimize can be written in this:

$$
L(\theta)=L\left(\int_{t_{0}}^{t_{1}} f(\mathbf{z}(t), t, \theta) d t\right)
$$

and we want to know 

$$
\frac{\partial L}{\partial \theta}
$$

Simplily do backpropagration in ODE-net is a bad idea for two reasons:

1. High memory cost.
2. Extra numerical error.

There is another easier and more efficient method introduced in this paper, and the basic idea is to do back propagration via another ode solver.

## Adjoint state and Reverse-mode derivative(backpropagration) of an ODE initial value problem

To optimize $$L$$ one needs to compute the gradients wrt. its parameters: $$z(t_0), t_0, t_1, \theta$$.

$$z(t_0)$$ is the hidden state at time $$t_0$$

The first term $$
\frac{d L}{d \mathbf{z}(t)}
$$ is called **Adjoint** in this paper. The adjoint state is actually the gradient with respect to the hidden state at a specified time t and the dynamic of adjoint can actually be modeled by another ODE:

 
$$
\frac{d \mathbf{a}(t)}{d t}=-\mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}
$$

**Proof**

With a continuous hidden state, we can write the transformation after an $$\varepsilon$$ change in time as 

$$
\mathbf{z}(t+\varepsilon)=\int_{t}^{t+\varepsilon} f(\mathbf{z}(t), t, \theta) d t+\mathbf{z}(t)=T_{\varepsilon}(\mathbf{z}(t), t)
$$

By chain rule we have 

$$
\frac{d L}{\partial \mathbf{z}(t)}=\frac{d L}{d \mathbf{z}(t+\varepsilon)} \frac{d \mathbf{z}(t+\varepsilon)}{d \mathbf{z}(t)}
$$

or 

$$
\mathbf{a}(t)=\mathbf{a}(t+\varepsilon) \frac{\partial T_{\varepsilon}(\mathbf{z}(t), t)}{\partial \mathbf{z}(t)}
$$

follow the definition of derivative:


 $$
\begin{aligned} \frac{d \mathbf{a}(t)}{d t} &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon) \frac{\partial}{\partial \mathbf{z}(t)} T_{\varepsilon}(\mathbf{z}(t))}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon) \frac{\partial}{\partial \mathbf{z}(t)}\left(\mathbf{z}(t)+\varepsilon f(\mathbf{z}(t), t, \theta)+\mathcal{O}\left(\varepsilon^{2}\right)\right)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{\mathbf{a}(t+\varepsilon)-\mathbf{a}(t+\varepsilon)\left(I+\varepsilon \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial z(t)}+\mathcal{O}\left(\varepsilon^{2}\right)\right)}{\varepsilon} \\ &=\lim _{\varepsilon \rightarrow 0^{+}} \frac{-\varepsilon \mathbf{a}(t+\varepsilon) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}+\mathcal{O}(\varepsilon)}{\varepsilon \mathbf{z}(t)}+\mathcal{O}(\varepsilon) \\ &=-\mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)} \end{aligned}
$$

To get the value of $$a(t)$$, we simply need to call a ODE solver and specify the constraint on the last time
point (any value defined by us) .The adjoint at $$t_0$$ is 

$$
\mathbf{a}\left(t_{0}\right)=\mathbf{a}\left(t_{N}\right)+\int_{t_{N}}^{t_{0}} \frac{d \mathbf{a}(t)}{d t} d t=\frac{d L}{d \mathbf{z}\left(t_{N}\right)}-\int_{t_{N}}^{t_{0}} \mathbf{a}(t)^{T} \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)}
$$

Similarly, the derivative with respect to $$\theta$$ can be also calculated. First we set $$
\frac{d L}{d \theta}=\mathbf{a}_{\theta}\left(t_{N}\right) = 0
$$


$$
\begin{aligned}
\frac{d L}{d \theta}&= \frac{d L}{d z(t)} \frac{d z(t)}{d \theta} \\ &=
\int_{t_{0}}^{t_{N}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \mathbf{z}(t)} \frac{\partial \mathbf{z}(t)}{\partial \theta}\\ &= \int_{t_{0}}^{t_{N}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial \theta}
\end{aligned}
$$



Finally, we also get gradients with respect to $$t_0$$ and $$t_N$$ , the start and end of the integration interval.

$$
\frac{d L}{d t_{N}}=\mathbf{a}\left(t_{N}\right) f\left(\mathbf{z}\left(t_{N}\right), t_{N}, \theta\right) \quad \frac{d L}{d t_{0}}=\mathbf{a}_{t}\left(t_{0}\right)=\mathbf{a}_{t}\left(t_{N}\right)-\int_{t_{N}}^{t_{0}} \mathbf{a}(t) \frac{\partial f(\mathbf{z}(t), t, \theta)}{\partial t} d t
$$


To calculate all these derivatives in one single call of ODE solver, the author wrap up everything into one variable called augmented state. The details are in original paper Appendix B.2.

## Some key code in implemetation.

1. Ode solver (the author used Euler method and it can be replaced by more sophiscated ode solver)

```python
def ode_solve(z0, t0, t1, f):
    """
    Simplest Euler ODE initial value solver
    """
    h_max = 0.05
    n_steps = math.ceil((abs(t1 - t0)/h_max).max().item())

    h = (t1 - t0)/n_steps
    t = t0
    z = z0

    for i_step in range(n_steps):
        z = z + h * f(z, t)
        t = t + h
    return z
```

2. Augmented dynamics computation (to get the derivatives described in above section)

```python
class ODEF(nn.Module):
    def forward_with_grad(self, z, t, grad_outputs):
        """Compute f and a df/dz, a df/dp, a df/dt"""
        batch_size = z.shape[0]

        out = self.forward(z, t)

        a = grad_outputs
        adfdz, adfdt, *adfdp = torch.autograd.grad(
            (out,), (z, t) + tuple(self.parameters()), grad_outputs=(a),
            allow_unused=True, retain_graph=True
        )
        # grad method automatically sums gradients for batch items, we have to expand them back 
        if adfdp is not None:
            adfdp = torch.cat([p_grad.flatten() for p_grad in adfdp]).unsqueeze(0)
            adfdp = adfdp.expand(batch_size, -1) / batch_size
        if adfdt is not None:
            adfdt = adfdt.expand(batch_size, 1) / batch_size
        return out, adfdz, adfdt, adfdp

    def flatten_parameters(self):
        p_shapes = []
        flat_parameters = []
        for p in self.parameters():
            p_shapes.append(p.size())
            flat_parameters.append(p.flatten())
        return torch.cat(flat_parameters)
```

3. ODE-net

```python
class ODEAdjoint(torch.autograd.Function):
    @staticmethod
    def forward(ctx, z0, t, flat_parameters, func):
        assert isinstance(func, ODEF)
        bs, *z_shape = z0.size()
        time_len = t.size(0)

        with torch.no_grad():
            z = torch.zeros(time_len, bs, *z_shape).to(z0)
            z[0] = z0
            for i_t in range(time_len - 1):
                z0 = ode_solve(z0, t[i_t], t[i_t+1], func)
                z[i_t+1] = z0

        ctx.func = func
        ctx.save_for_backward(t, z.clone(), flat_parameters)
        return z

    @staticmethod
    def backward(ctx, dLdz):
        """
        dLdz shape: time_len, batch_size, *z_shape
        """
        func = ctx.func
        t, z, flat_parameters = ctx.saved_tensors
        time_len, bs, *z_shape = z.size()
        n_dim = np.prod(z_shape)
        n_params = flat_parameters.size(0)

        # Dynamics of augmented system to be calculated backwards in time
        def augmented_dynamics(aug_z_i, t_i):
            """
            tensors here are temporal slices
            t_i - is tensor with size: bs, 1
            aug_z_i - is tensor with size: bs, n_dim*2 + n_params + 1
            """
            z_i, a = aug_z_i[:, :n_dim], aug_z_i[:, n_dim:2*n_dim]  # ignore parameters and time

            # Unflatten z and a
            z_i = z_i.view(bs, *z_shape)
            a = a.view(bs, *z_shape)
            with torch.set_grad_enabled(True):
                t_i = t_i.detach().requires_grad_(True)
                z_i = z_i.detach().requires_grad_(True)
                func_eval, adfdz, adfdt, adfdp = func.forward_with_grad(z_i, t_i, grad_outputs=a)  # bs, *z_shape
                adfdz = adfdz.to(z_i) if adfdz is not None else torch.zeros(bs, *z_shape).to(z_i)
                adfdp = adfdp.to(z_i) if adfdp is not None else torch.zeros(bs, n_params).to(z_i)
                adfdt = adfdt.to(z_i) if adfdt is not None else torch.zeros(bs, 1).to(z_i)

            # Flatten f and adfdz
            func_eval = func_eval.view(bs, n_dim)
            adfdz = adfdz.view(bs, n_dim) 
            return torch.cat((func_eval, -adfdz, -adfdp, -adfdt), dim=1)

            
        dLdz = dLdz.view(time_len, bs, n_dim)  # flatten dLdz for convenience
        with torch.no_grad():
            ## Create placeholders for output gradients
            # Prev computed backwards adjoints to be adjusted by direct gradients
            adj_z = torch.zeros(bs, n_dim).to(dLdz)
            adj_p = torch.zeros(bs, n_params).to(dLdz)
            # In contrast to z and p we need to return gradients for all times
            adj_t = torch.zeros(time_len, bs, 1).to(dLdz)

            for i_t in range(time_len-1, 0, -1):
                z_i = z[i_t]
                t_i = t[i_t]
                f_i = func(z_i, t_i).view(bs, n_dim)

                # Compute direct gradients
                dLdz_i = dLdz[i_t]
                dLdt_i = torch.bmm(torch.transpose(dLdz_i.unsqueeze(-1), 1, 2), f_i.unsqueeze(-1))[:, 0]

                # Adjusting adjoints with direct gradients
                adj_z += dLdz_i
                adj_t[i_t] = adj_t[i_t] - dLdt_i

                # Pack augmented variable
                aug_z = torch.cat((z_i.view(bs, n_dim), adj_z, torch.zeros(bs, n_params).to(z), adj_t[i_t]), dim=-1)

                # Solve augmented system backwards
                aug_ans = ode_solve(aug_z, t_i, t[i_t-1], augmented_dynamics)

                # Unpack solved backwards augmented system
                adj_z[:] = aug_ans[:, n_dim:2*n_dim]
                adj_p[:] += aug_ans[:, 2*n_dim:2*n_dim + n_params]
                adj_t[i_t-1] = aug_ans[:, 2*n_dim + n_params:]

                del aug_z, aug_ans

            ## Adjust 0 time adjoint with direct gradients
            # Compute direct gradients 
            dLdz_0 = dLdz[0]
            dLdt_0 = torch.bmm(torch.transpose(dLdz_0.unsqueeze(-1), 1, 2), f_i.unsqueeze(-1))[:, 0]

            # Adjust adjoints
            adj_z += dLdz_0
            adj_t[0] = adj_t[0] - dLdt_0
        return adj_z.view(bs, *z_shape), adj_t, adj_p, None
```















